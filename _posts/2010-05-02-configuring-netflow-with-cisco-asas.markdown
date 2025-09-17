---
layout: post
title: Configuring Netflow on ASA's
categories:
- cisco
- scripts
- asa
- netflow
comments: true
---
Netflow is data that the ASA will send to a netflow collector which will then give details regarding bandwith used, top talkers, number of connections, etc. Unfortunately there aren't any show commands on the ASA to determine this. In other words no data is stored on the ASA regarding netflow. Instead it is sent real time to the collector.

Netflow is supported on ASA version 8.1 and later. Note that v8.1 was for 5580's only. Version 8.2.x is available to any ASA. It uses netflow version 9.

## ASA Config

### Define the collector(s)
Port 9996 is the default port.<br>
`flow-export destination INSIDE 172.16.200.101 9996`

Indicate how often (in minutes) to send the template to the collector<br>
`flow-export template timeout-rate 30`

Delay transmission of flow creation events for short lived flows in seconds.
This is optional. If omitted from the configuration there will not be any delay.<br>
`flow-export delay flow-create 3`

### Define the Traffic to be Collected
** To send netflow info for all traffic **

`policy-map global_policy`<br>
`class class-default`<br>
`flow-export event-type all destination 172.16.200.101`

** To send netflow for specific traffic **<br>
Create an ACL for desired traffic<br>
`access-list ACL-FLOW-EXPORT extended permit ip 172.16.200.0 255.255.255.0 any`

Create a class-map<br>
`class-map CLASS-NETFLOW`<br>
`  match access-list ACL-FLOW-EXPORT`

Add the class to whatever global service policy is on the firewall
The IP must match one of the defined netflow collectors from earlier.<br>
`policy-map global_policy`<br>
`  class CLASS-NETFLOW`<br>
`flow-export event-type flow-create destination 172.16.200.101`


### Show Command 
The only show command is just to verify it is sending netflows<br>
`show flow-export counters`


## Sample Netflow Output

Sample output from a linux netflow collector using nfcap and nfdump:

```
[linuxbox]~$ nfdump -T -r 2010/01/17/nfcapd.201001171035 -a  -A srcip,srcport,dstip,dstport -o extended -c 20
nfdump filter:
any
Date flow start          Duration Proto      Src IP Addr:Port          Dst IP Addr:Port   Flags Tos  Packets    Bytes      pps      bps    Bpp Flows
2010-01-17 10:34:39.616   179.955     0     172.16.100.1:41212 -&gt;   172.16.200.101:9996  .A....   0        5      884        0       39    176     5
2010-01-17 10:35:34.285     0.252     0   172.16.200.102:57274 -&gt;   174.129.41.112:80    .AP.SF   0        7     1562       27    49587    223     1
2010-01-17 10:35:34.365     0.256     0   174.129.41.112:80    -&gt;   172.16.200.102:57274 .AP.SF  32        6     1103       23    34468    183     1
2010-01-17 10:31:09.471   300.156     0     172.16.100.1:0     -&gt;        224.0.0.5:0     .A.... 192       31     2108        0       56     68     1
2010-01-17 10:36:34.352     0.320     0   172.16.200.102:57275 -&gt;   174.129.41.112:80    .AP.SF   0        7     1562       21    39049    223     1
2010-01-17 10:36:34.436     0.320     0   174.129.41.112:80    -&gt;   172.16.200.102:57275 .AP.SF   0        6     1103       18    27574    183     1
2010-01-17 10:36:36.912     0.020     0     66.102.7.100:80    -&gt;   172.16.200.102:57267 .A...F   0        2      104       99    41599     52     1
2010-01-17 10:36:36.912     0.000     0   172.16.200.102:57267 -&gt;     66.102.7.100:80    .A...F   0        2      104        0        0     52     1
2010-01-17 10:36:47.088     6.698     0     66.102.7.100:80    -&gt;   172.16.200.102:57269 .A...F   0        2      104        0      124     52     2
2010-01-17 10:36:47.092     6.676     0   172.16.200.102:57269 -&gt;     66.102.7.100:80    .A...F   0        2      104        0      124     52     1
2010-01-17 10:37:34.543     0.308     0   172.16.200.102:57279 -&gt;   174.129.41.112:80    .AP.SF   0        7     1562       22    40571    223     1
2010-01-17 10:37:34.623     0.332     0   174.129.41.112:80    -&gt;   172.16.200.102:57279 .AP.SF   0        6     1103       18    26578    183     1
2010-01-17 10:37:34.531     0.000     0   172.16.200.102:62395 -&gt;     68.105.28.11:53    .A....   0        1       76        0        0     76     1
2010-01-17 10:37:34.539     0.000     0     68.105.28.11:53    -&gt;   172.16.200.102:62395 .A....   0        1      275        0        0    275     1
2010-01-17 10:38:35.529     0.316     0   172.16.200.102:57281 -&gt;   174.129.41.112:80    .AP.SF   0        7     1562       22    39544    223     1
Summary: total flows: 20, total bytes: 13316, total packets: 92, avg bps: 238, avg pps: 0, avg bpp: 144
Time window: 2010-01-17 10:31:09 - 2010-01-17 10:39:36
Total flows processed: 27, Records skipped: 0, Bytes read: 1416
Sys: 0.000s flows/second: 0.0        Wall: 0.000s flows/second: 156069.4
```
