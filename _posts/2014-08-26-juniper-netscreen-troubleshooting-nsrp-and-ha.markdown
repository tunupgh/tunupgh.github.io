---
layout: post
title: Juniper Netscreen troubleshooting NSRP and HA
date: 2014-08-26 15:47
comments: true
categories:
- troubleshooting
- juniper
- netscreen
- nsrp
---
Some basic commands to help troubleshoot NSRP (failover/high availability) with Juniper Netscreen SSG devices.

### Review the NSRP configuration

Here is a config pull from a working HA firewall config.

```
netscreen:netscreen-cluster-pri(M)-> get config | i nsrp
set nsrp cluster id 1
set nsrp cluster name netscreen-cluster
set nsrp rto-mirror sync
set nsrp rto-mirror route
set nsrp vsd-group id 0 priority 1
set nsrp vsd-group id 0 preempt
set nsrp vsd-group id 0 preempt hold-down 20
set nsrp secondary-path ethernet0/1
set nsrp monitor interface ethernet0/0
set nsrp monitor interface ethernet0/1
set nsrp monitor interface ethernet2/6
set nsrp monitor interface ethernet2/5
set nsrp monitor interface ethernet2/4
set nsrp monitor interface ethernet2/0
set nsrp monitor interface ethernet2/1
```

### Examine the current NSRP state

Some commands to examine the state of the situation

```
netscreen:netscreen-pri(M)-> get nsrp vsd-group all

VSD group info:
init hold time: 8
heartbeat lost threshold: 3
heartbeat interval: 1000(ms)
master always exist: disabled
group priority preempt holddown inelig   master       PB other members
    0        1 yes           20 no       myself 13691648
total number of vsd groups: 1
Total iteration=16523170,time=603985353,max=119017,min=2779,average=36

vsd group id: 0, member count: 2, master: 13690368
member information:
---------------------------------------------------------------------
group  unit_id  state          prio flag rto_peer   hb miss holddown
---------------------------------------------------------------------
    0 13691648  primary backup  100    0        0    1    0        3
    0 13690368  master            1    2        0    0    0       20
```

Some interesting things to get out of this command:

`master = myself` This is good to know who the current active or master firewall is.

`member count = 2` If this is 2 it means there is another firewall joined to this VSD group.

Another command is simply `get nsrp`. This will show the *which is the HA interface*, as well as other information.


### Determine the last time a failover occurred

The only way to do this is to check the logs.

```
netscreen:netscreen-pri(M)-> get event | incl nsrp
2014-08-25 14:03:39 system crit  00015 NSRP: HA control channel change to 
2014-08-25 14:03:27 system crit  00015 NSRP: HA control channel change to 
2014-08-25 14:01:44 system crit  00015 NSRP: HA control channel change to 
2014-08-25 14:00:53 system crit  00015 NSRP: HA control channel change to
```

This only tells half the story. From here we can see that the syslog type is 00015. Let's take a look at the logs filtering on that log type only:

```
netscreen:netscreen-pri(M)-> get event type 00015
get event type 00015
Date       Time     Module Level  Type Description
2014-08-25 14:03:39 system crit  00015 NSRP: HA control channel change to 
                                       ethernet2.
2014-08-25 14:03:27 system crit  00015 NSRP: HA control channel change to 
                                       NULL.(disconnected).
2014-08-25 14:01:44 system crit  00015 NSRP: HA control channel change to 
                                       ethernet2.
2014-08-25 14:00:53 system crit  00015 NSRP: HA control channel change to 
                                       NULL.(disconnected).
2014-08-25 14:00:49 system crit  00015 NSRP: HA control channel change to 
                                       ethernet2.
2014-08-25 14:00:41 system crit  00015 NSRP: HA control channel change to 
                                       NULL.(disconnected).
```

### Initiate a manual failover

Here is the command to conduct a failover. Execute this from the primary firewall:

`exec nsrp vsd-group 0 mode backup`


### Additional problems

From the primary firewall there is not a way to see what the IP is of the backup firewall. The primary firewall will utilize the IP addresses in the config. The one exception to this is the management IP. This is configured seperately on each firewall in the cluster.

There is not a way to view the logs in the backup device from the primary.
