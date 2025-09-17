---
layout: post
title: Troubleshooting High CPU on a Cisco ASA
date: 2014-04-28 19:03
comments: true
categories:
- cisco
- asa
- cpu
- splunk
---
Is your ASA having a High CPU issue? Here's some methods for troubleshooting the issue.


## Find out what process is causing the CPU to be high

To see what the current CPU usage is:

```
asa# show cpu usage
CPU utilization for 5 seconds = 94%; 1 minute: 92%; 5 minutes: 92%
```

* Under normal conditions the CPU should stay below 50% (baseline as per network); if the CPU reaches 100% the firewall will start dropping packets
* FWSM CPU is used for limited traffic processing; during ACL compilation CPU is expected to be near 100% until ACL is compiled
* The show cpu usage command displays the CPU over time as a running average

Now take a look at what the top process is that's causing it.

```
asa# show processes cpu-usage sorted non-zero
PC           Thread        5Sec     1Min     5Min    Process
0x08298b79   0x6e5d4e14    82.1%    82.3%    83.6%   Dispatch Unit
0x090f20ad   0x6e5ca0b0     0.1%     0.0%     0.0%   ssh
0x09192b79   0x6e5bd330     0.1%     0.0%     0.0%   snmp
0x08ca2340   0x6e5cdca0     0.1%     0.1%     0.1%   Unicorn Admin Handler
0x0913e27c   0x6e5cb1d0     0.1%     0.1%     0.1%   Logger
0x09155cba   0x6e5a71fc     0.0%     0.0%     0.4%   ssh
```

Notice what process is taking up the most of the CPU. In this case it's Dispatch Unit.

This command was first Introduced in Cisco ASA Version 7.2(4.11), 8.0(4.5), 8.1(1.100), 8.2(1)50


## Troubleshooting High CPU related to Dispatch Unit

In short, dispatch unit is the process that processes traffic. In general when this is high it means that traffic is overwhelming the firewall and the firewall can't keep up. This could be due to too much traffic hitting a specific ACL, policy, class or other ASP drop reason.

If you have a high CPU due to dispatch unit you first must identify what traffic is causing this. If you normally don't have a high CPU then it shouldn't be too hard to identify what traffic is causing this problem.

Let's start by examining the following show commands:

`show interface`
Do you see any input or output errors? If so, take a look at the [meaning of interface counters](http://www.tunnelsup.com/understanding-cisco-asa-interface-counters-and-statistics/) post to determine what the drops are.

`show traffic`
Does any interface have an unusually high amount of packets/bytes going through it?

`show perfmon`
Does any stat seem crazy high?

`show service-policy`
Are any of the inspects rising very quickly?


Another thing to use is Splunk or a syslog collector to determine what is happening on the device. If you are using Splunk to collect logs from this ASA you could do a search like so:

`<ASA-IP>  | stats count by error_code event_desc | sort 10 -count `

This will show you something like this:

```
error_code  event_desc                                                                                                      count
419002        Received duplicate TCP SYN with different initial sequence number.                                                87874
106023        Deny protocol src by access_group acl_ID                                                                        7390
305013        Asymmetric NAT rules matched for forward and reverse flows; Connection denied due to NAT reverse path failure.    618
420003        IPS requested to reset TCP connection from ifc_in:SIP/SPORT to ifc_out:DIP/DPORT                                439
420002        IPS requested to drop ICMP packets ifc_in:SIP to ifc_out:DIP (typeICMP_TYPE, code ICMP_CODE)                    73
```

In the case above you can see that syslog message 419002 is triggering a crazy amount of syslogs. Investigate that syslog message to find what the biggest traffic flow is that is responsible for that. You could do a Splunk search like so:

`<ASA-IP> error_code="419002" | stats count by src_ip dest_ip dest_port | sort -count`

With a search like that you may get a result like this:

```
src_ip             dest_ip         dest_port     count
10.21.21.21        10.100.100.1    8530        80598
192.168.49.168    192.168.200.112    80            237
192.168.49.168    192.168.200.112    443            235
```

Now you know that the flow between `10.21.21.21` and `10.100.100.1` is doing a large amount of SYN flooding. Try shunning that source IP for a while to see if traffic dies down. Or find the offending user and tell them to stop.



	

