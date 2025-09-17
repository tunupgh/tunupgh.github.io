---
layout: post
title: Cisco ASA TCP Randomization Issue
date: 2016-03-29 23:21
comments: true
categories:
- cisco
- asa
- troubleshooting
---
You may sometimes see this syslog message from a Cisco ASA:

`%ASA-4-419002: Received duplicate TCP SYN from in_interface : src_address / src_port to out_interface : dest_address / dest_port with different initial sequence number.`

I see this a lot on VPN firewalls where packets are dropped due to the sequence numbers not being correct in TCP.  This happens when the ASA randomizes the TCP sequence numbers and another device is also performing the same randomization of the TCP sequence numbers.

 

One way to bypass this is to disable TCP Sequence Number randomization on the ASA.  This can be done on a selective basis.  (NOTE:  You may need this as well as Option 19 permissions if you are putting BGP through the firewall).

 

Here is a sample conf to disable TCP randomization for a specific subnet:

 
```
access-list ACL-SEQUENCE-NUMBER extended permit tcp vpn-ip-pool any
access-list ACL-SEQUENCE-NUMBER extended permit tcp any vpn-ip-pool

class-map CM-SEQUENCE-NUMBER
  match access-list ACL-SEQUENCE-NUMBER

policy-map global_policy
class CM-SEQUENCE-NUMBER
  set connection random-sequence-number disable
```

