---
layout: post
title: Making Postfix RFC Compliant
date: 2015-02-14 20:47
comments: true
categories:
- misc
- postfix
---
By default [Postfix](http://www.postfix.org/) is not RFC compliant. Here are some helpful tips to getting your Postfix installation conform to the email RFCs.

### What is RFC?

Mainly this document is covering [RFC 2821](http://tools.ietf.org/html/rfc2821). This is publication created by the Internet Engineering Task Force (IETF) which aims to standardize how the SMTP protocol should behave. By adhering to the standard you will be promoting best practices and taking steps to safeguarding your mail server from abusers.


### Require HELO 
In the `/etc/postfix/main.cf` add the following:

```
smtpd_helo_required = yes
```

This makes sure that clients introduce themselves properly before continuing. It means the first command accepted from the client is "EHLO" or "HELO".

### SMTP Recipient Restrictions

Again in `main.cf` add the following lines:

```
smtpd_recipient_restrictions = 
 reject_non_fqdn_sender
 reject_non_fqdn_recipient
 reject_unknown_sender_domain
 reject_unknown_recipient_domain
 permit_mynetworks
 reject_unauth_destinations
 reject_multi_recipient_bounce
 reject_non_fqdn_hostname
 reject_invalid_hostname
 permit
```

If you have other permits such as `permit_sasl_authenticated`, that should go right after `permit_mynetowrks`. 

The [smtp_recipient_restrictions](http://www.postfix.org/postconf.5.html#smtpd_recipient_restrictions) command specifies what mail postfix should receive. This actually applies to both incoming and outgoing mail because in either destination postfix still has to make the decision on whether to accept the message or not. 

The way this command works is that the checks stop on the first match. The default policy at the end is to permit everything else. We add the permit statement so this is more clear. Our goal is to block all traffic that does not conform to RFC standards or isn't on our network or isn't a legitimate user. If the message passes all the reject checks then it finally gets permitted.

The order of operation is important because if you notice up to and including the first permit these checks will be applied to both internal and external users. The permits in the middle (in case you added sasl permit too) will permit any internal user or authenticated user trying to send mail to somewhere else. If there is a match on these permits then the rest of the checks never get checked. The remaining reject checks will only be applied to external clients trying to send mail inbound to Postfix.



##### reject_non_fqdn_sender

The RFC requires a fully qualified domain names for both sender and recipient. This command will reject 'sender' or 'from' fields that contain partial domain names. For instance with this line, Postfix will reject mail from `example@example`. The domain portion of this address is not a FQDN and probably should have been written as `example@example.com`.

##### reject_non_fqdn_recipient

This command will reject the 'recipient' or 'to' fields from containing a partial domain name in the server portion of the address. 

##### reject_unknown_sender_domain

This setting will attempt to do a DNS lookup on the 'sender' or 'from' domain name. If the 'from' field states `example@no.such.domain` the Postfix server will reject this because it cannot resolve `no.such.domain`.

##### reject_unknown_recipient_domain

This setting will attempt to do a DNS lookup on the 'recipient' or 'to' domain name.

##### reject_unauth_destinations

This setting will check if recipient domain is something that Postfix responds to.

##### reject_non_fqdn_hostname

This setting requires clients to complete the HELO with their FQDN. The RFC requires an FQDN be presented in the initial HELO.

##### reject_invalid_hostname

This setting will reject hostnames that are incomplete or use invalid characters. This step is checked at the initial HELO.




### Mandatory accounts

According to both RFC2821 and [RFC2142](http://tools.ietf.org/html/rfc2142) these addresses should be valid.

* postmaster
* abuse
* webmaster (if running a webserver)
* hostmaster (if running a DNS server)

That is if your mail domain is example.com you must accept mail for `postmaster@example.com` to be compliant.


