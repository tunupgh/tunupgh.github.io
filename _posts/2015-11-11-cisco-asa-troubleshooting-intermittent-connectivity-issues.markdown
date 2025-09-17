---
layout: post
title: Cisco ASA Troubleshooting Intermittent Connectivity Issues
date: 2015-11-11 22:19
comments: true
categories:
- cisco
- asa
- troubleshooting
---
Normally a Cisco ASA firewall either permits or denies traffic. It's very rare that traffic works sometimes but not all the time. Here are some troubleshooting tips for when the ASA is causing intermittent or sporadic connectivity issues.

## Embryonic Connections issues
Check the [show conn](/understanding-cisco-asa-connection-flags/) output to see if there are a lot of half opened or embryonic connections.

Is the number of embryonic connections very high? This could be filling up the max connection table on the ASA or on the destination system. 

You can set the max embryonic connections with this:

```
access-list 140 extended permit tcp any host 192.168.1.50 eq www
!
class-map protect
 description Protect web server from attacks
 match access-list 140
!
policy-map interface_policy
 class protect
  set connection embryonic-conn-max 100 per-client-max 25
!
service-policy interface_policy interface outside
```

This sets a maximum amount of connections to 192.168.1.50 on port 80 to be 100 connections total and 25 per source IP. The `per-client-max 25` statement is optional at the end of the `set connection` command.

## Check bandwidth
If the bandwidth of an interface is hitting the max capacity it will drop packets. 

Use the `show traffic` command to verify no interface is nearing the limit.

Look at `show int` and confirm the BW setting, and duplex settings are correct. 


## Check CPU
If the CPU reaches 100% it will start dropping packets.

[Troubleshoot any high CPU issues.](/troubleshooting-high-cpu-on-a-cisco-asa)


## Check interface and memory statistics
[Check interface statistics](/understanding-cisco-asa-interface-counters-and-statistics/) and look for any dropped packet counters rising.

Check the [show blocks](/cisco-asa-understanding-the-show-blocks-command/) for memory usage issues.

Some notes on packets, performance, and memory:

> If ingress FIFO (queue) is full, frames are dropped
> 
> - No free slots in RX ring (CPU/memory bound)
> - Unable to acquire bus (used by another component)
> - “No buffer” on memory move errors, “overruns” on FIFO drops
> 
> 
> FIFO is not affected by packet rates, but RX rings are
> 
> - Fixed memory block size regardless of actual frame size
> - Ingress packet bursts may cause congestion even at low bits/sec
> 
> Fixed bus overhead for memory transfers
> 
> - 30% or 80% bus efficiency for 64 or 1400 byte packets
> - Maximize frame size and minimize rate for best efficiency
> 
> Overruns on all interfaces may mean several things
> 
> - Interface oversubscription
> - CPU oversubscription on a single core system
> - Uneven CPU load distribution on a multi-core system
> - Memory block exhaustion

