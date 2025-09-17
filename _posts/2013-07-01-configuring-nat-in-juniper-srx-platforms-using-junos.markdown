---
layout: post
title: Configuring NAT in Juniper SRX Platforms using JunOS
date: 2013-07-01 11:13
comments: true
categories:
- juniper
- nat
- scripts
---
There are 3 kinds of NAT for the JunOS SRX devices. Source NAT, destination NAT, and static NAT. We will also cover Proxy ARP. This post will only cover the basic and most common concepts and usage of NAT in SRX. But with this knowledge you should be able to do NAT for almost any occasion.

This post assumes you know the basic concept of NAT and what it is and why we use it and why we need it.

## JunOS NAT packet processing

Consider the image below of how a packet is flowing through an SRX in regards to NAT.
![NAT flow IMG](/images/junos-nat-packet-flow.png)

In order for the SRX to process the policies, it first needs to know what zone the packet is trying to get to. That is why the destination of a static NAT and destination NAT takes place first. After the policy is evaluated then the source of the static NAT or source NAT gets translated.

As you might have guessed from looking at the diagram, static NAT takes precedence over any other kind of NAT.

If there are overlapping NAT rules, the most specific (longest prefix length) is used.


## Source NAT
This generally refers to outgoing NAT only. Traffic from within your company that has private IPs that needs to get out to the internet is source NAT'd to a public IP.

**Note:** This NAT is UNIDIRECTIONAL. Meaning, traffic will be NAT'd one way, and be able to reply back, but it will not NAT going the other direction.

![NAT Source IMG](/images/junos-nat-source.png)

The config to conduct source NAT would look something like this:

```
set security nat source rule-set NAT-DMZ-TO-UNTRUST from zone DMZ
set security nat source rule-set NAT-DMZ-TO-UNTRUST to zone UNTRUST

set security nat source rule-set NAT-DMZ-TO-UNTRUST rule PAT-INTERFACE match source-address 192.168.0.0/16
set security nat source rule-set NAT-DMZ-TO-UNTRUST rule PAT-INTERFACE match destination-address 0.0.0.0/0
set security nat source rule-set NAT-DMZ-TO-UNTRUST rule PAT-INTERFACE then source-nat interface
```
To create a NAT you must create a `rule-set` and a `rule` within that rule-set. Only one rule-set can be applied on a zone pair. Multiple rules can then be applied in that rule-set.

In the example above, the traffic coming from 192.168.0.0/16 that is destined for anywhere that is traveling from the DMZ zone to the UNTRUST zone will be source NAT'd to the interface of the UNTRUST interface IP address. In this particular case we are PAT'ing the traffic, also known as NAT overload. This is where many IPs can use the same single source IP as they are NAT'd through the firewall. 

### Then Source-NAT Options
In the NAT config above you see the `then source-nat` portion last. There are three options that can be added here. In the case above we use the option `interface`. There is also `pool` and `off`.

#### Pool Option

Suppose you don't want to NAT to the interface but instead you want to use another IP. To first do that you must define that IP in a `pool`. The configuration would look like so:

```
set security nat source pool POOL-PAT address 199.199.199.199/32

set security nat source rule-set NAT-DMZ-TO-UNTRUST from zone DMZ 
set security nat source rule-set NAT-DMZ-TO-UNTRUST to zone UNTRUST

set security nat source rule-set NAT-DMZ-TO-UNTRUST rule PAT-INTERFACE match source-address 192.168.0.0/16
set security nat source rule-set NAT-DMZ-TO-UNTRUST rule PAT-INTERFACE match destination-address 0.0.0.0/0
set security nat source rule-set NAT-DMZ-TO-UNTRUST rule PAT-INTERFACE then source-nat pool POOL-PAT 
```

Even if you only have 1 IP you still make a pool. Take note that the pool will either be defined a source pool or destination pool. 

If you have over 64,000 connections going through the firewall into a single IP, you can have multiple IP addresses in the pool and the SRX will alternate between the IP addresses defined in the pool.

#### Off Option
It is possible to identify traffic that you specifically **don't** want to NAT. This would be useful if you are NAT'ing everything coming from DMZ going to UNTRUST, but you didn't want to NAT a specific flow that is supposed to go over a VPN tunnel. To conduct something like that you would use the `off` option. Here is an example:

```
set security nat source rule-set NAT-DMZ-TO-UNTRUST from zone DMZ
set security nat source rule-set NAT-DMZ-TO-UNTRUST to zone UNTRUST

set security nat source rule-set NAT-DMZ-TO-UNTRUST rule NAT-OFF match source-address 192.168.0.0/16
set security nat source rule-set NAT-DMZ-TO-UNTRUST rule NAT-OFF match destination-address 172.16.57.0/24
set security nat source rule-set NAT-DMZ-TO-UNTRUST rule NAT-OFF then source-nat off

set security nat source rule-set NAT-DMZ-TO-UNTRUST rule PAT-INTERFACE match source-address 192.168.0.0/16
set security nat source rule-set NAT-DMZ-TO-UNTRUST rule PAT-INTERFACE match destination-address 0.0.0.0/0
set security nat source rule-set NAT-DMZ-TO-UNTRUST rule PAT-INTERFACE then source-nat interface
```
Notice there are two rules. The first is called `NO-NAT` which specifically says `source-nat off` if the traffic matches the criteria. That is because this traffic is going over a VPN and we don't want it to be NAT'd but everything else must be source NAT'd to the interface IP.

This is the Cisco equivalent to doing a NAT Zero, NAT 0, No NAT, or Identity NAT.

## Destination NAT
Destination NAT is usually referring to incoming NAT. Specifically someone on the internet needs to access a device inside the network can be destination NAT'd to be reachable from the outside.

**Note:** This NAT is UNIDIRECTIONAL. Meaning, traffic will be NAT'd one way, and be able to reply back, but it will not NAT going the other direction.

![NAT Source IMG](/images/junos-nat-dest.png)

This type of NAT is likely to be used for the outside public network to get to a webserver which has a private IP. As the traffic comes into the SRX the destination IP will have to be changed from the public IP to the real IP or private IP. 

```
set security nat destination pool POOL-WEBSERVER address 10.10.10.10/32

set security nat destination rule-set NAT-UNTRUST-TO-DMZ from zone UNTRUST 
set security nat destination rule-set NAT-UNTRUST-TO-DMZ to zone DMZ

set security nat destination rule-set NAT-UNTRUST-TO-DMZ rule DEST-NAT match destination-address 199.199.199.199/32
set security nat destination rule-set NAT-UNTRUST-TO-DMZ rule DEST-NAT then destination-nat pool POOL-WEBSERVER
```
In the example above any traffic coming into the SRX on the UNTRUST zone that is destined for 199.199.199.199 will be destination translated to go to 10.10.10.10. Keep in mind, 10.10.10.10 can respond to this traffic due to the SRX being a stateful firewall, however if it initiates traffic it will not be source NAT'd to 199.199.199.199.

If there are multiple IPs in the pool, the SRX will load balance between the IP's in the pool. Sending some traffic to each of the IPs in the destination pool. Considered the poor mans way to load balance.

### Port Forwarding
Sometimes you want to do port forwarding. This would be classified as destination NAT. Here is an example of that.

```
set security nat destination pool POOL-PORT-FORWARD address 10.10.10.10/32 port 80

set security nat destination rule-set NAT-UNTRUST-TO-DMZ from zone UNTRUST 
set security nat destination rule-set NAT-UNTRUST-TO-DMZ to zone DMZ

set security nat destination rule-set NAT-UNTRUST-TO-DMZ rule DEST-NAT match destination-address 199.199.199.199/32
set security nat destination rule-set NAT-UNTRUST-TO-DMZ rule DEST-NAT match destination-port 8080
set security nat destination rule-set NAT-UNTRUST-TO-DMZ rule DEST-NAT then destination-nat pool POOL-PORT-FORWARD
```
In the above example, anyone coming into this SRX from the UNTRUST interface trying to get to 199.199.199.199 on port 8080, will be translated to go to 10.10.10.10 on port 80. 


## Static NAT
This type of NAT is bidirection. It is one to one. Specifically one public IP can be NAT'd to one private IP. This would essentially create a source and destination NAT all in one rule. 

**Note:** This NAT is BIDIRECTIONAL. Meaning, traffic will be NAT'd both if it's going out or coming in.

![NAT Source IMG](/images/junos-nat-static.png)

Because this is bidirectional, you would use this type of NAT if you'd like to map a public IP to a private IP to allow traffic to that IP to be destination NAT'd and traffic from that IP to be source NAT'd.

Configuration example:

```
set security nat static rule-set STATIC-SERVER1 from zone UNTRUST
set security nat static rule-set STATIC-SERVER1 rule RULE-NAME match destination-address 99.99.99.99/32
set security nat static rule-set STATIC-SERVER1 rule RULE-NAME then static-nat 10.10.10.99/32
```

## Proxy ARP
If you are doing NAT and the destination is not the IP address of the interface of the SRX, then you need to tell the SRX to do proxy ARP. 

Imagine the firewall has a UNTRUST IP of 99.99.99.99 and is doing a static NAT for 99.99.99.88 to be NAT'd to 10.10.10.88. In this case next hop internet router will do an ARP to look for whoever owns 99.99.99.88. By default the SRX will not respond to this ARP request. Proxy ARP must be enabled for the SRX to say "oh, that packet is for me, here's my MAC address".

Here is an example of proxy ARP configuration:

```
set security nat proxy-arp interface ge-0/0/0.0 address 99.99.99.88/32 to 99.99.99.99/89/32
```
Now the SRX will 'listen' for any ARP requests for 99.99.99.88 and 99.99.99.89.



## Show Commands
Here are a few show commands to watch how NAT is taking place as it passes through the firewall.

`show security flow session`<br>
`show security nat source summary`<br>
`show security destination source summary`<br>
