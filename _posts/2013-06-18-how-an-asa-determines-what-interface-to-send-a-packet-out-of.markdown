---
layout: post
title: How an ASA determines what interface to send a packet out of
date: 2013-06-18 18:16
comments: true
categories:
- cisco
- asa
- firewall
- troubleshooting
---
Sometimes I see the question "Why is NAT choosing what interface to send the packet out of on a Cisco ASA?" or "Since when do NAT rules make routing decisions?" or "What is the `route-lookup` keyword in the NAT configuration for?" or "Why is the firewall saying no route to host but there's a route in the routing table?"

This is a tricky question but I believe it's best understood in a two step process. It all comes down to the order of operation of a firewall and how the firewall processes packets. Most importantly the NAT rules and route lookups.

If you refer to my post on [ASA Order of Operations](/tup/2013/06/18/cisco-asa-order-of-operation/) you might already have a clue as to what is going on.

## How a firewall processes NAT
When the packet is being processed by the NAT rules, the firewall has to know what the egress interface of the packet is in order to understand what NAT rule to apply. Usually the packet can match a NAT rule which has a ingress and egress interface defined in the rule. The firewall trusts the NAT rule to properly identify the egress interface for the packet. If you rather the firewall look at the routing table instead of the NAT rule, specify the `route-lookup` keyword at the end of the NAT rule (applies to 8.3+). After it does the XLATE stuff to the packet, it then *virtually* forwards the packet to the egress interface.

## What happens to the packet when it's on the egress interface?
Once the packet is on the egress interface the firewall does a route lookup on that packet to determine what interface it needs to go out of. The only option the firewall will accept is the egress interface it's already on. If it happens to be any other interface the packet is likely to be dropped with the syslog message `ASA-6-110001: No route to 111.111.11.11 from 10.0.5.5` or one saying reverse path check failure. 


Source: [Cisco documentation](http://www.cisco.com/en/US/docs/security/asa/asa84/configuration/guide/nat_overview.html#wpxref17243)
