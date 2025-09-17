---
layout: post
title: How to see the routing table on Mac OSX
date: 2016-09-20 20:39
comments: true
categories:
- mac
- osx
---
Do you have a Macbook Pro or Macbook Air and want to know how to show the routes it has in the routing table? Are you looking for the Windows command **route print** equivalent in OSX? This post is for you.

## Show Routes

This is the command to show routes on a Mac OSX:

```
netstat -rn
```

The `-r` flag means to show routes.

The `-n` flag means to not resolve IPs to hostnames.


Here is an example output of the command:

```
$ netstat -rn
Routing tables

Internet:
Destination        Gateway            Flags        Refs      Use   Netif Expire
default            192.168.20.1       UGSc           39        0     en0
127.0.0.1          127.0.0.1          UH              3    11132     lo0
192.168.20/24      link#4             UCS             8        0     en0
192.168.20.1       0:1f:ca:88:96:8c   UHLWIir        40       22     en0   1025
192.168.20.255     ff:ff:ff:ff:ff:ff  UHLWbI          0        8     en0
```

In the case above, the default gateway of this Mac is 192.168.20.1. Meaning all routes are pointing to that system.



