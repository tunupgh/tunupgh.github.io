---
layout: post
title: What is a Firewall?
date: 2014-11-09 10:29
comments: true
categories:
- misc
- firewall
---
A firewall is a physical device or software that provides a layer of security into a network or computer. Its primary task is to only permit traffic that is required to pass through and block all other traffic. 

![](/images/firewall1.png)

In the average home network, traffic will likely not originate from the internet and be destined for the home network. However, traffic is often seen originating from within the home network going out to the internet. A firewall could be deployed to block all incoming traffic and permit all outgoing traffic. This would block many attempts from hackers trying to enter the home network. 

A firewall can be installed as a software program on a single computer. This type of firewall is often limited to only blocking traffic in or out of the computer. For instance in the Windows operating system a firewall is installed by default. This will block traffic coming into the computer but allow traffic to leave the computer.

Many companies like to put a firewall on the edge of their network. This is a physical device that sits between the public internet and the private internal network. These types of physical firewalls are created by such companies as Cisco or Juniper. 

There are three reasons to deploy a firewall in the network: access control, NAT and VPN. Access control is where policies are created to block or permit traffic based on IP address, port and protocol. NAT, or network address translation, is the act of converting one IP address to another. A firewall often is equipped to do many kinds of NAT which would be translating the private internal IP addresses to publicly accessible IP addresses. Firewalls also often have VPN capabilities. A Virtual Private Network is a technology used to securely connect two networks together across an insecure network such as the internet. 


#### What is a Stateful Firewall?
Firewalls are often stateful. This means the firewall will remember the connection going through it so it can permit return traffic for the same flow. If the firewall is set up to block all incoming traffic but allow all outgoing traffic, then how will any return traffic come back through that is requested by a machine on the inside of the network? The stateful firewall will keep track of what requests originated from the inside so that when the traffic comes back from the outside it will be permitted.


#### What is a Next Gen Firewall?
A next generation or NG firewall is loosely defined but often refers to firewalls that can inspect traffic that isn't just on layer 3 of the OSI model. Layer 3 is IP addresses, ports and protocols. NG firewalls not only are able to inspect this traffic but they can also look inside the packet and determine if the packet is for a particular website or even a particular application on a website. An NG firewall may be able to allow users to go to Facebook but block their ability to chat on Facebook. This would mean that the firewall is able to operate at layer 7 of the OSI model.





