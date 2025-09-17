---
layout: post
title: Cisco ASA and SLA Monitoring
date: 2015-04-16 09:06
comments: true
categories:
- cisco
- asa
- scripts
---
The Cisco ASA firewall can do three basic SLA monitoring tasks. They are:

1. Continuously ping from the ASA even when nobody is logged in
2. Change routes based on IP ping reachability
3. Alert via syslog or SNMP when the SLA monitor fails

Unfortunately the ASA only has the ability to ping for its sla monitoring and is pretty limited in its capabilities.

## Continuously ping from the ASA
Suppose for some reason you wish to have the ASA send a constant ping to something. This could be helpful to keep a tunnel alive, or send constant ping for some reason. This is actually required when making a [VPN tunnel to Amazon AWS](/troubleshooting-vpn-between-cisco-asa-and-amazon-aws).

Suppose we want the ASA to ping `93.184.216.34` every `3` seconds with a `1000` milliseconds (1 second) timeout. Here is the config for that:

```
sla monitor 100
  type echo protocol ipIcmpEcho 93.184.216.34 interface OUTSIDE
    timeout 1000
    frequency 3
sla monitor schedule 100 life forever start-time now
```

In this case our SLA monitor ID is `100` and this will go forever starting now. There are some other options here to only have this go for a limited amount of pings or start at a different time.

## Change routes based on IP ping availability

Let's now build on the `sla monitor` commands above and add route tracking. First we need to make a tracking object. We can do that like this:

```
track 1 rtr 100 reachability
```

This will create a track ID of `1` and track sla monitor `100` for reachability.

Next we need to add the tracking to a route.

```
route OUTSIDE 93.184.216.34 255.255.255.255 95.95.95.95 1 track 1
```
Here we are telling the ASA to use this static route ONLY if the sla monitor pings are successful. If the monitor starts failing then the route will be **removed** from the routing table. The `1 track 1` command says this route has a weight of `1` and this will be in the routing table if `track 1` is up.

We can set a fallback route for the ASA in the event that the sla monitor is failing. We can simply add a static route with a higher metric like so:

```
route OUTSIDE 93.184.216.34 255.255.255.255 21.21.21.21 20
```

With these two routes in place, the one with a weight of `1` will take precedence. Then when the ping fails it will be removed and the route with a weight of `20` will take over.


## Alert via syslog or SNMP when the SLA monitor fails
When you have `sla monitor`, `track`, and a `route` with a track, syslogs will be created when the track changes state. You must have **all** of these things configured in order to see the syslogs.

When the track goes down, this is the syslog shown:

```
%ASA-6-622001: Removing tracked route 93.184.216.34 255.255.255.255 95.95.95.95, distance 1, table default, on interface OUTSIDE
```

When the track comes back up this is the syslog shown:

```
%ASA-6-622001: Adding tracked route 93.184.216.34 255.255.255.255 95.95.95.95, distance 1, table default, on interface OUTSIDE
```


### Turn the syslogs into SNMP traps
You can turn the syslog into an SNMP trap by doing the following config:

```
logging list SLA-LIST message 622001
logging trap SLA-LIST
logging history SLA-LIST
snmp-server enable traps syslog
```

You'll see the following trap when the track fails.

```
Apr 15 14:53:42 172.16.1.1
community=secretstring
enterprise=1.3.6.1.4.1.9.9.41.2
snmp_version=2
generic-trap=6
specific-trap=1
varBindTotal=7
sysUpTime="128 days, 1:02:27"
snmp:1.1.4.1.0=1.3.6.1.4.1.9.9.41.2.0.1
E:9.9.41.1.2.3.1.2.0=20
E:9.9.41.1.2.3.1.3.0=7
E:9.9.41.1.2.3.1.4.0="Syslog Trap"
E:9.9.41.1.2.3.1.5.0=0x3f3c3136363e254153412d362d3632323030313a2052656d6f76696e6720747261636b656420726f7574652039332e3138342e3231362e3334203235352e3235352e3235352e3235352039352e39352e39352e39352c2064697374616e636520312c207461626c652064656661756c742c206f6e20696e74657266616365204f555453494445603f3f223f3f
E:9.9.41.1.2.3.1.6.0="128 days, 1:02:27"
srcIP=172.16.1.1
```

The varbind `E:9.9.41.1.2.3.1.5.0` is hex encoded. Using a hex to ascii converter it then looks like this:

`<166>%ASA-6-622001: Removing tracked route 93.184.216.34 255.255.255.255 95.95.95.95, distance 1, table default, on interface OUTSIDE`


## Show commands

The following commands will show the state of the SLA monitor.

`show sla monitor operational-state`

`show track`



### Additional reading

http://www.cisco.com/c/en/us/td/docs/security/asa/asa-command-reference/S/cmdref3/s15.html#pgfId-1557524

http://www.cisco.com/c/en/us/td/docs/security/asa/asa-command-reference/T-Z/cmdref4/t2.html#pgfId-1565770
	

	
