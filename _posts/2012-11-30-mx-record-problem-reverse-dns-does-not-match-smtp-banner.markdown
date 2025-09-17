---
layout: post
title: 'MX Record problem: Reverse DNS does not match SMTP Banner'
categories:
- troubleshooting
- asa
- banner
- cisco
- dns
- ironport
- mx
- mxrecord
- mxtoolbox
- receiving aborted
- smtp
- smtp reverse dns mismatch
comments: true
---
Today I was given a problem that our Cisco Ironport was not accepting email from outside people sending mail to inside people (backstory: this occurred right after we moved our Ironport to a different location). I looked in Ironport and spotted a lot of messages failing to be delivered. Specifically the error in Ironport was:
<code>Message 1006902 aborted: Receiving aborted</code>

I spent a long time doing packet captures to try to troubleshoot and determined the remote end was sending a reset which made me think this is the sender's problem and not mine. However I was wrong.

The next thing I checked was the MX Record at [MXToolbox.com](http://mxtoolbox.com) (a great site for looking up DNS records and stuff). Specifically the SMTP test showed this:

![MX failing - IMG](/images/mxfail.png)

Specifically I didn't like seeing this warning:
<strong>SMTP Reverse DNS Mismatch --- Warning - Reverse DNS does not match SMTP Banner</strong>
and
<strong>SMTP TLS --- Warning - Does not support TLS</strong>

But what does that mean? I specifically wanted to know what two strings are being compared that resulted in a mismatch. Well in the case above the two strings it was comparing were <strong>\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*</strong> and <strong>mail3.example.com</strong>. For some reason this took me a long time to realize the \*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\* was the banner... You can see it in the image above after 220.

Looking around on the internet it turns out that our Cisco ASA we have in front of the Ironport has `inspect esmtp` turned on (which is on by default). 

Issue the following commands to turn off the inspect:

```
policy-map global_policy
 class inspection_default
  no inspect esmtp
```

Upon turning that inspect off the issue immediately cleared up and the results were this:

![MX passing - IMG](/images/mxpass.png)

Mail was then flowing into the Ironport properly and being delivered as expected. Looking back at the problem if I would have looked at the logs in the ASA I would have seen these syslogs:

`%ASA-4-108004: ESMTP Classification: Dropped connection for ESMTP Request from outside:75.75.75.75/35314 to DMZ:10.0.25.101/25; matched Class 4: header line length gt 998`

`%ASA-4-507003: tcp flow from outside:75.75.75.75/35314 to PUBLIC_DMZ:10.0.25.101/25 terminated by inspection engine, reason - inspector disconnected, dropped packet.`
