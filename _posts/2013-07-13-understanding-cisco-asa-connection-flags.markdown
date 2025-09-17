---
layout: post
title: Understanding Cisco ASA Connection Flags
date: 2013-07-13 10:33
comments: true
categories:
- cisco
- asa
- troubleshooting
- connection
- flags
---
Have you ever wondered what the flags meant when you issued the `show conn` or `show connections` command? This post will demystify that for you.

## Viewing the Connections
Use the command `show conn` to view the connections currently going through the firewall. It should look something like this:

```
TCP VPN 10.99.55.44(18.17.16.15):11515 inside 10.88.77.66:30854, idle 0:02:48, bytes 178, flags UIO
TCP outside 77.66.55.44:49368 VPN 15.15.15.15:443, idle 0:00:21, bytes 100531, flags UfrIOB
UDP VPN 10.17.17.17:8500 inside 10.20.20.20:4167, idle 0:01:38, bytes 616, flags -
TCP VPN 77.66.55.44:30031 inside 10.20.20.20:51716, idle 0:00:11, bytes 0, flags U
TCP outside 10.20.20.20:10101 outside 10.30.30.30:4450, idle 0:00:14, bytes 0, flags SaAB
TCP outside 20.30.40.50:45174 inside 10.30.30.30:443, idle 0:00:05, bytes 0, flags aB
```
 
## Understanding the Flags
In earlier versions of Cisco ASA versions it used to list the following table when issuing the `show conn` command.
 
- A - awaiting inside ACK to SYN
- a - awaiting outside ACK to SYN
- B - initial SYN from outside
- C - CTIQBE media
- D - DNS
- d - dump
- E - outside back connection
- F - outside FIN
- f - inside FIN
- G - group
- g - MGCP
- H - H.323
- h - H.225.0
- I - inbound data
- i - incomplete
- k - Skinny media
- M - SMTP data
- m - SIP media
- O - outbound data
- P - inside back connection
- q - SQL*Net data
- R - outside acknowledged FIN
- R - UDP RPC
- r - inside acknowledged FIN
- S - awaiting inside SYN
- s - awaiting outside SYN
- T - SIP
- t - SIP transient
- U - up
 
## Adding it up
Often a single flag isn't seen, but multiple flags will be displayed. Use this handy guide to understand flag combinations.
 
![ASA Conn Flags IMG](/images/asa-conn-flags.png)
 
The green circles in the image above indicates two way traffic is seen for that connection which means the connection is good and healthy usually.
 
## Case Studies
Let's look at a few examples. Suppose you see the lines in the 'show conn' output.
 

```
TCP VPN 10.99.55.44(18.17.16.15):11515 inside 10.88.77.66:30854, idle 0:02:48, bytes 178, flags UIO
```
This traffic flow has completed the 3 way TCP handshake (U), has had both inbound (I) packet and outbound (O) packets.

---
<br>
 
 
```
TCP outside 77.66.55.44:49368 VPN 15.15.15.15:443, idle 0:00:21, bytes 100531, flags UfrIOB
```
This traffic flow originated from the outside (B), has completed the 3 way TCP handshake (U), has had both inbound (I) packet and outbound (O) packets. This flow also saw a fin packet sent to the inside (f) and the inside also acknowledged the fin (r).

---
<br>

```
UDP VPN 10.17.17.17:8500 inside 10.20.20.20:4167, idle 0:01:38, bytes 616, flags -
```
This flow has no flags because it's a UDP packet and therefore is stateless.
 
---
<br>


```
TCP VPN 77.66.55.44:30031 inside 10.20.20.20:51716, idle 0:00:11, bytes 0, flags U
```
This flow is just completing the 3 way handshake (U).
 
---
<br>


```
TCP inside 10.20.20.20:10101 outside 10.30.30.30:4450, idle 0:00:14, bytes 0, flags SaAB
```
A SYN was sent from 10.20.20.20 on the inside to 10.30.30.30 on the outside.
 

---
<br>

```
TCP outside 20.30.40.50:45174 inside 10.30.30.30:443, idle 0:00:05, bytes 0, flags aB
```
A SYN+ACK has been seen coming from the outside coming in.

 
