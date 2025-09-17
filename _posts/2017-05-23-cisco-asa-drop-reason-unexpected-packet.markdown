---
layout: post
title: 'Cisco ASA Drop Reason: Unexpected-Packet'
date: 2017-05-23 21:05
comments: true
categories:
- cisco
- asa
- troubleshooting
---
Today I was trying to send management traffic over a VPN tunnel to a Cisco ASA that terminated the tunnel. I ran into some problems doing this and I want to document my troubleshooting steps.

### Config

Before attempting to ssh to the ASA I made sure that ssh to the ASA worked when coming from the inside of the network.

Then I applied the config:

```
ssh 10.0.0.0 255.0.0.0 INSIDE
management-access INSIDE
```

Even though we are coming from the OUTSIDE, when it comes over the VPN tunnel we can get into the ASA through the INSIDE interface. Going over the VPN tunnel for ssh is more secure than doing it directly over the Internet. This also means you'll be sshing to the IP that is on the INSIDE of your ASA.

### Errors

Well this didn't work and to see the drop reason we can do a asp-drop capture like this:

`cap cap1 type asp-drop all`

Then I looked at the cap including just the IP I was after and saw this:

```
ASA5508# sh cap cap1 | i 192.168.128.5
   9: 17:40:03.527957       10.50.101.6 > 192.168.128.5: icmp: echo request Drop-reason: (unexpected-packet) Unexpected packet
  10: 17:40:03.641858       10.2.11.50.49955 > 192.168.128.5.161:  udp 75 Drop-reason: (unexpected-packet) Unexpected packet
  17: 17:40:05.520206       10.50.101.6 > 192.168.128.5: icmp: echo request
  24: 17:40:07.320402       10.2.11.50 > 192.168.128.5: icmp: echo request Drop-reason: (unexpected-packet) Unexpected packet
  58: 17:40:22.546907       10.50.101.6.1030 > 192.168.128.5.22: S 1027314954:1027314954(0) win 4128 <mss 536> Drop-reason: (unexpected-packet) Unexpected packet
  64: 17:40:24.540224       10.50.101.6.1030 > 192.168.128.5.22: S 1027314954:1027314954(0) win 4128 <mss 536> Drop-reason: (unexpected-packet) Unexpected packet
  67: 17:40:25.106302       10.2.11.50.39489 > 192.168.128.5.161:  udp 41 Drop-reason: (unexpected-packet) Unexpected packet
  77: 17:40:28.541292       10.50.101.6.1030 > 192.168.128.5.22: S 1027314954:1027314954(0) win 4128 <mss 536> Drop-reason: (unexpected-packet) Unexpected packet
  84: 17:40:30.111276       10.2.11.50.39489 > 192.168.128.5.161:  udp 41 Drop-reason: (unexpected-packet) Unexpected packet
```

Now we have narrowed down this issue to be a drop reason of "unexpected-packet".

### Fix

[Cisco Writes:](http://www.cisco.com/c/en/us/td/docs/security/asa/asa-command-reference/show_asp_drop/show_asp_drop.html)

> Unexpected-Packet occurs when the appliance in transparent mode receives a non-IP packet, destined to its MAC address, but there is no corresponding service running on the appliance to process the packet.

But in my case this is neither a non-IP packet, nor is this firewall in transparent mode. So because Cisco is flat out wrong here, a blog post was obligatory.

We actually need to take a look at our **NAT** commands. Specifically adding the `route-lookup` option to our NAT.

My **NONAT** statement looked like this:

`nat (INSIDE,any) source static LOCAL-NETS LOCAL-NETS destination static REMOTE-NETS REMOTE-NETS`

This was changed to become:

`nat (INSIDE,any) source static LOCAL-NETS LOCAL-NETS destination static REMOTE-NETS REMOTE-NETS route-lookup`

After that, the ssh worked.


### Reason

[Cisco Writes the route-lookup command:](http://www.cisco.com/c/en/us/td/docs/security/asa/asa-command-reference/I-R/cmdref2/n.html)

> For identity NAT in routed mode, determines the egress interface using a route lookup instead of using the interface specified in the NAT command. If you do not specify interfaces in the NAT command, a route lookup is used by default.

That means, if you have `any` in your NAT statement, the ASA isn't sure what interface to route the packet to (yes, even though the ASA HAS this IP on it's interface). By adding `route-lookup` the ASA decides to check the routing table to determine what interface to send the packet to. 
