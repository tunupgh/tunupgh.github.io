---
layout: post
title: Troubleshooting high CPU on Juniper SRX Junos devices
date: 2015-09-30 19:20
comments: true
categories:
- juniper
- cpu
- srx
- junos
---
Occasionally a Juniper SRX device running Junos will have a high CPU. Here are some tips for troubleshooting these incidents.

## Validate

Check the routing engine (control plane). Check the CPU status by doing `show chassis routing-engine`.

```
user@USPHIFW1> show chassis routing-engine
Routing Engine status:
    Temperature                 40 degrees C / 104 degrees F
    CPU temperature             38 degrees C / 100 degrees F
    Total memory              1024 MB Max   758 MB used ( 74 percent)
      Control plane memory     560 MB Max   442 MB used ( 79 percent)
      Data plane memory        464 MB Max   316 MB used ( 68 percent)
    CPU utilization:
      User                      90 percent
      Background                 0 percent
      Kernel                     6 percent
      Interrupt                  0 percent
      Idle                       4 percent
    Model                          RE-SRX240H
    Serial ID                      AAEM9236
    Start time                     2014-02-23 10:25:39 CST
    Uptime                         584 days, 7 hours, 58 minutes, 37 seconds
    Last reboot reason             0x1:power cycle/failure
    Load averages:                 1 minute   5 minute  15 minute
                                       0.46       0.48       0.49
```

Above you can see that the CPUs are 4% idle which means it's 96% utilized. I would say anything over 90% is considered bad. Once the CPU gets gets to 100% utilization it will start dropping packets and possibly overheating.

Next you want to look further and see what processes are running high. Do this with the command `show system processes extensive`.

```
user@USPHIFW1> show system processes extensive
last pid: 15924;  load averages:  0.50,  0.50,  0.50  up 584+08:03:00    19:28:16
149 processes: 19 running, 115 sleeping, 3 zombie, 12 waiting

Mem: 172M Active, 140M Inact, 539M Wired, 73M Cache, 112M Buf, 46M Free Swap:

  PID USERNAME       THR PRI NICE   SIZE    RES STATE  C   TIME   WCPU COMMAND
 1306 nobody           2 139    0  8996K  3440K RUN    0  20.5H 7518.75% httpd
 1377 root             7  76    0   499M 52316K select 0    ??? 281.15% flowd_octeon_hm
 1106 root             1  87    0 13692K  3200K RUN    0 1712.2 22.51% eventd
15922 root             1   4    0  7732K  2968K sbwait 0   0:00  2.25% sshd
```

Usually even under good conditions, there will be processes that are running at well over 100% utilization. The Junos does a terrible job at adding in this case, something to do with multi core processors confusing the output. You can do `start shell` then `top -H` to see the actual utilization per core.


## Analyze the processes

Now that you know what processes are running high, we can look into why it's causing it.

### Process: httpd

If you see the process `httpd` as one of the **first three** processes with the highest CPU, chances are the web UI is having issues and needs to be restarted. Restarting this process only impacts any user that are currently in the web UI of this SRX.

To restart the httpd process run the following command:

```
restart web-management
```

This will immediately restart the process without confirmation. After doing so, look at `show chassis routing-engine` over and over to see if the percent idle has gone up over 30%. If so, that has fixed your problem.

I very frequently see this process get stuck at a high percent. I'm not sure what causes it, but the fix is quick and easy so that's nice.

### Process: eventd

If the process `eventd` is running high (over 20%) then this is probably something worth looking into. This process handles the events on the Juniper device itself which includes:

* Storing internal syslog messages
* Sending syslog messages to another system
* Sending/responding to SNMP traps/polls
* Sampling handling
* Traceoptions handling

If this is running high check if any of the above are turned on a little too high. Perhaps too many traceoptions are on, or too much sampling is turned on. Try turning these off and see if the CPU goes back to normal.

There are two modes for syslogs, event and stream. Perhaps changing it to stream will reduce the CPU utilization.

### Process: flowd_octeon

The process `flowd_octeon` seems to always run over 200%. This is normal. Usually this isn't the problem and try looking at the next highest CPU hog as the culprit.

This processes is responsible for packet handling, data processing, or flow processing. The flow processing is all done on the data plane.

##### Check the packet forwarding engine (data plane)
The following two commands shows us what's happening on the data plane.

```
user@USPHIFW1> show chassis forwarding
FWDD status:
  State                                 Online
  Microkernel CPU utilization         5 percent
  Real-time threads CPU utilization   0 percent
  Heap utilization                   68 percent
  Buffer utilization                  1 percent
  Uptime:                               584 days, 8 hours, 43 minutes, 30 seconds
```

```
user@USPHIFW1> show security monitoring fpc 0
FPC 0
  PIC 0
    CPU utilization      :    2 %
    Memory utilization   :   68 %
    Current flow session :  929
    Max flow session     : 131072
Session Creation Per Second (for last 96 seconds on average):    0
```

If the CPU utilization here is low, then you don't have a problem with the data plane.

[The data plane (aka forwarding plane)](http://www.juniper.net/documentation/en_US/junos12.1/topics/concept/chassis-cluster-data-plane-understanding.html) is where the SRX decides what to do with the packet. This is where the SRX looks at the forwarding table and routing table to determine where to send the packet. If your CPU here is high, then it's possible you are reaching the capacity of this device. Start looking at things like how many packets and bytes each interface is receiving and comparing it with the model specifications.

To examine the throughput of each interface use the following command:

`show interfaces detail | match "link is Up| bps| pps" | except "0 bps|0 pps"`

To examine the number of sessions use the following command:

`show security flow statistics`

Check the model for limitations here:

[http://www.juniper.net/us/en/products-services/security/srx-series/compare/#a=SRX100,SRX110,SRX210,SRX220,SRX240,SRX300,SRX550,SRX650,SRX1400,SRX1500,SRX3400,SRX3600,SRX5400,SRX5600,SRX5800](http://www.juniper.net/us/en/products-services/security/srx-series/compare/#a=SRX100,SRX110,SRX210,SRX220,SRX240,SRX300,SRX550,SRX650,SRX1400,SRX1500,SRX3400,SRX3600,SRX5400,SRX5600,SRX5800)
	

