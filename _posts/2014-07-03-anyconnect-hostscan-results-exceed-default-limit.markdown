---
layout: post
title: AnyConnect Hostscan results exceed default limit
date: 2014-07-03 20:09
comments: true
categories:
- cisco
- anyconnect
- hostscan
- troubleshooting
---
I have a user who is unable to login using AnyConnect. Their screen hangs at the AnyConnect message: “Hostscan is waiting for the next scan”.
 
Looking at the logs on the ASA I saw the following log:

`Jun 25 2014 16:25:21: %ASA-3-716600: Rejected 266KB Hostscan data from IP <10.5.1.50>. Hostscan results exceed default limit of 200KB.`

[The syslog database](http://www.cisco.com/c/en/us/td/docs/security/asa/syslog-guide/syslogs/logmsgs.html#pgfId-7474251) says to increase the limit I need to contact Cisco TAC. So I did. 

Cisco TAC gave me the following commands which fixed the issue:

```
ASA(config)# service internal
ASA(config)# webvpn
ASA(config)# hostscan data-limit <size-in-kilobytes>
```

Additional questions I asked Cisco but there was no answer:

- Why is this a secret command?
- My current hostscan only checks one registry string. Why is it generating more than 200KB of data for this?
- Why is the default setting not good enough?
- Why is this only happening for some users?
 