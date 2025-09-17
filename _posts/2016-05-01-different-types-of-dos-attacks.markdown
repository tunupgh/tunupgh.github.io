---
layout: post
title: Different Types of DoS Attacks
date: 2016-05-01 11:06
comments: true
categories:
- infosec
- dos
- attacks
---
A Denial of Service attack or DoS attack is where the adversary disrupts the target's cyber application. This could be taking down their server, overwhelming a network, or causing a system to break. There a few different ways this can be achieved.

To protect against a DoS you must first understand the multiple types of DoS style attacks.

## Network Volume Based DoS

A volume based DoS attack occurs when the attacker overwhelms the bandwidth of the target. When the target's bandwidth is completely consumed by the attacker's traffic, legitimate traffic will not get through.

This sometimes occurs accidentally when an unexpected surge of network traffic comes to the servers and they can't handle it. 

## Connection Based DoS

The connection based DoS occurs when the number of connections to a system becomes maxed out at the server or any network device in the path. For instance, if the Apache web server is only configured to accept 25 connections, that can easily be filled which would result with the 26th connection being dropped by the server. If an attacker overwhelms the number of connections, legitimate traffic will not be able to get through.

## Application Based DoS

Applications being served online aren't always bug free. Without proper implementation of security inside the application, an attacker could manipulate the code to cause the application to crash. Sometimes devices can occur a buffer overflow which could result in a reload or process hang or system crash. If an attacker can cause the targets systems to crash by sending a certain string or input or packet, this would be an application based DoS attack.






