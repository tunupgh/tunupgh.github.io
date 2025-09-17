---
layout: post
title: Packet capture for Cisco IOS Router
date: 2015-11-11 23:05
comments: true
categories:
- cisco
- ios
- router
---
Having the ability to conduct packet captures is a valuable tool for troubleshooting connectivity issues within a network. IOS routers 12.4(20)T and up has the Embedded Packet Capture (EPC) built in to it.

[How to do packet captures on a Cisco ASA](/packet-captures-on-cisco-asa/)

[How to do packet capture on Cisco Nexus OS](/cisco-ios-packet-captures-with-ethanalyzer/)


### Routers

(All commands can be done from the exec mode except for creating the ACL which requires config mode)


Define the capture point/interface/direction

```
monitor capture point ip cef CAPTURE FastEthernet0 both
```
 

Define the buffer and size and how to react when it fills up

```
monitor capture buffer CAPBUF size 512 max-size 1024 circular
```
 

Create an ACL to hit on the interesting traffic and assign it to the capture buffer

```
monitor capture buffer CAPBUF filter access-list ACL-CAP
```
 

Associate the capture point with the capture buffer

```
monitor capture point associate CAPTURE CAPBUF
```
 

Start the packet capture

```
monitor capture point start CAPTURE
```

To stop it: 

```
monitor capture point stop CAPTURE
```
 

Now show what you’ve collected:

```
show monitor capture buffer CAPBUF [dump]
```
 

It is probably easier to read this in wireshark so here is how to export it:

```
monitor buffer export CAPBUF tftp://192.168.1.21/myrouter.pcap
```
 


