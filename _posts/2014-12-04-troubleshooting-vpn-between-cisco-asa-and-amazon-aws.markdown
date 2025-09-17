---
layout: post
title: Troubleshooting VPN between Cisco ASA and Amazon AWS
date: 2014-12-04 13:28
comments: true
categories:
- cisco
- aws
- troubleshooting
---
Recently I had to create a VPN tunnel from a Cisco ASA running 9.2.2 code to an Amazon AWS instance.

I was able to build the tunnel and get it established but it would only work if traffic originated from the ASA side towards AWS. If AWS tried to initiated the tunnel it would not come up. Specifically I saw these errors in the logs:

```
Dec 04 2014 12:37:51: %ASA-5-713119: Group = 77.88.99.100, IP = 77.88.99.100, PHASE 1 COMPLETED
Dec 04 2014 12:37:51: %ASA-3-713061: Group = 77.88.99.100, IP = 77.88.99.100, Rejecting IPSec tunnel: no matching crypto map entry for remote proxy 0.0.0.0/0.0.0.0/0/0 local proxy 0.0.0.0/0.0.0.0/0/0 on interface OUTSIDE
Dec 04 2014 12:37:51: %ASA-3-713902: Group = 77.88.99.100, IP = 77.88.99.100, QM FSM error (P2 struct &0x00007fff33763a70, mess id 0x2c44baec)!
Dec 04 2014 12:37:51: %ASA-3-713902: Group = 77.88.99.100, IP = 77.88.99.100, Removing peer from correlator table failed, no match!
Dec 04 2014 12:37:51: %ASA-5-713259: Group = 77.88.99.100, IP = 77.88.99.100, Session is being torn down. Reason: crypto map policy not found
Dec 04 2014 12:37:51: %ASA-4-113019: Group = 77.88.99.100, Username = 77.88.99.100, IP = 77.88.99.100, Session disconnected. Session Type: LAN-to-LAN, Duration: 0h:00m:00s, Bytes xmt: 0, Bytes rcv: 0, Reason: crypto map policy not found
```

Phase 1 was establishing correctly but the interesting traffic wasn't matching any crypto map I had defined so it wouldn't create Phase 2.

We can understand this by analyzing the error message "IP = 77.88.99.100, Rejecting IPSec tunnel: no matching crypto map entry for remote proxy 0.0.0.0/0.0.0.0/0/0 local proxy 0.0.0.0/0.0.0.0/0/0 on interface OUTSIDE".

The peer we are trying to connect to is 77.88.99.100.

The interface this is coming in on is our OUTSIDE interface.

The "0.0.0.0/0.0.0.0/0/0" is telling us that the remote side has something else defined in their VPN traffic definition. Our next steps is to compare our ACL with the remote side's ACL or VPN traffic definition.


### Fix the ACL!

Here was my tunnel ACL:

```
access-list ACL-AWS-VPN extended permit ip 10.0.202.0 255.255.255.0 10.1.2.0 255.255.255.0
access-list ACL-AWS-VPN extended permit ip 10.0.204.0 255.255.255.0 10.1.2.0 255.255.255.0
```

As you can see, there are two subnets on the ASA side that I want in the tunnel. However, when making a tunnel to AWS, **THIS WILL NOT WORK!** Amazon AWS requires only a single line to be on the tunnel ACL. I was able to change the line to just be this:

```
access-list ACL-AWS-VPN extended permit ip any4 10.1.2.0 255.255.255.0
```

Here is AWS's explanation of why this is:

> This access list should contain a static route corresponding to your VPC CIDR and allow traffic from any subnet. If you do not wish to use the "any" source, you must use a single access-list entry for accessing the VPC range. If you specify more than one entry for this ACL without using "any" as the source, the VPN will function erratically. The any rule is also used so the security association will include the ASA outside interface where the SLA monitor traffic will be sourced from.


This still doesn't allow users on the AWS side to initiate the tunnel. This simply makes it so there is only one SA for this tunnel. The ASA needs to keep this tunnel up all the time so AWS can initiate traffic back to the ASA. To do this we have to use the `sla monitor` commands.


#### Optional: Restrict subnets you don't want in the tunnel
If using 'any' is too broad for your needs you can restrict traffic another way. By using tunnel-group filters. To go back to only permitting our only two subnets we can do this:

```
access-list ACL-AWS-FILTER extended permit ip 10.1.2.0 255.255.255.0 10.0.202.0 255.255.255.0
access-list ACL-AWS-FILTER extended permit ip 10.1.2.0 255.255.255.0 10.0.204.0 255.255.255.0
access-list ACL-AWS-FILTER extended deny ip any any
group-policy GRP-AWS-FILTER internal
group-policy GRP-AWS-FILTER attributes
  vpn-filter value ACL-AWS-FILTER
tunnel-group 77.88.99.100 general-attributes
  default-group-policy GRP-AWS-FILTER
exit
```

### Add an SLA monitor

Using sla monitor we can have the ASA do a continuous ping over the tunnel to keep it always up. Here's a config to ping an IP over the tunnel every 5 seconds, forever.

```
sla monitor 1
   type echo protocol ipIcmpEcho 10.1.2.2 interface OUTSIDE
   frequency 5
exit
sla monitor schedule 1 life forever start-time now
```

The point of this is only to keep the tunnel up. It doesn't matter if the pings are actually successful.



#### Further reading
[http://docs.aws.amazon.com/AmazonVPC/latest/NetworkAdminGuide/Cisco_ASA.html](http://docs.aws.amazon.com/AmazonVPC/latest/NetworkAdminGuide/Cisco_ASA.html)

[http://docs.aws.amazon.com/AmazonVPC/latest/NetworkAdminGuide/Cisco_ASA_Troubleshooting.html](http://docs.aws.amazon.com/AmazonVPC/latest/NetworkAdminGuide/Cisco_ASA_Troubleshooting.html)

