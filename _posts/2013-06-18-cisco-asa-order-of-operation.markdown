---
layout: post
title: Cisco ASA Order of Operation
date: 2013-06-18 17:39
comments: true
categories:
- cisco
- asa
- troubleshooting
---
This post will cover the order of operation that takes place in a Cisco ASA. Specifically the packet flow and each step that is conducted.

Consider the following image that displays the packet flow.

![ASA Order of Operation IMG](/images/asaorder.png)

1. Packet is received from the wire

1. Packet hits the ingress interface. Input counters are incremented.<br>
 **Inbound Packet Capture**: Packet is processed by any packet captures that exists on that interface

1. Does this packet have an existing connection?<br>
 If yes. Move ahead to step 6. <br>
 If no. If packet is TCP-SYN or UDP packet, proceed to ACL check. Else drop packet.

1. Packet is processed by inbound access-list. Notes about ACL checks:<br>
 First packet in flow is checked. All others are considered existing connections.<br>
 The first matching rule in the ACL is all that will be checked<br>
 ACL hitcnt will increment with matching rule

1. NAT rules process packet. Notes regarding NAT rules:<br>
 In post 8.3 nat control is turned off on the ASA and cannot be turned on. Pre 8.3 if nat control was on and a packet did not match an XLATE it was dropped. <br>
 A route lookup is conducted only to determine egress interface to match NAT rules<br>
 After translation takes place, the connection is created 

1. Packet is processed by any inspect rules.<br>
  *CSC Module*: Packet is processed by CSC module if firewall has it<br>
  *CX Module*: Packet is processed by CX module if firewall has it

1. Packet gets the IP address translated in the header. The port is also translated if the translation is a PAT. New checksums are created for packet.<br>
 **IPSM**: If IPS module is installed the packet is then passed to the module.

1. Packet is virtually forwarded to egress interface. Egress interface is determined first by translation rules if known.

1. L3 route check. Once on the egress interface a route check is performed. Only routes pointing to the egress interface will be forwarded. If you receive a syslog that looks like this `ASA-6-110001: No route to 111.111.11.11 from 10.0.5.5` the NAT and route have conflicting interfaces.

1. L2 address lookup. An ARP lookup is conducted at this stage.<br>
 **Outbound Packet Capture**: Packet is processed by any packet captures that exists on that interface

1. Packet is transmitted and put on wire. Interface counters go up. 


Source: Cisco Live Presentation in 2011, updated in 2012.
I have confirmed with a Cisco Technical Solutions Architect who got confirmation from a Cisco developer that this packet processing flow is valid for both pre 8.3 and post 8.3. 

## Data Flow
Here's another way to look at it which shows which plane the packet is on during which time.
![ASA Order of Operation IMG](/images/asaorder2.png)


