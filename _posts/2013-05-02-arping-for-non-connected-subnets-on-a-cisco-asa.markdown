---
layout: post
title: ARPing for non-connected subnets on a Cisco ASA
categories:
- arp
- asa
- cisco
- nat
- troubleshooting
comments: true
---
Consider the following network.

![ARP diagram - IMG](/images/arpnonconnected.png)

Things to notice:

Subnets 22.22.22.0/24 and 33.33.33.0/24 are being routed to the outside of the ASA.

There is a static NAT statement in the ASA to translate the real IP <span style="color: #ff0000;">192.168.5.22</span> to <span style="color: #ff0000;">22.22.22.22</span>

There is a static NAT statement in the ASA to translate the real IP<span style="color: #ff0000;"> 192.168.5.33</span> to <span style="color: #ff0000;">33.33.33.33</span>

<h2>So how do you get this to work properly?</h2>
In ASA pre-8.3 code the ASA would ARP for the static NATs it would have regardless if it's connected or not.

In ASA 8.3-8.4(4), THIS IS IMPOSSIBLE

In ASA 8.4(5)+ Cisco realized their major mistake and implemented the command:

`arp permit-nonconnected`
<h2>When else can I use this?</h2>
Another scenario to use this is when you have a router with multiple IPs on its interface that is connected to an ASA with a single IP. The ASA won't accept any packets for the other subnets that the router thinks is connected. By applying this command it will accept packets for the other subnets.

<h2>What's the risk?</h2>
By enabling this feature it could facilitate denial of service (DoS) attack against the ASA; a user on any interface could send out many ARP replies and overload the ASA ARP table with false entries. If you enable it be careful of what is physically plugging into the ASA since it can cause this problem.
