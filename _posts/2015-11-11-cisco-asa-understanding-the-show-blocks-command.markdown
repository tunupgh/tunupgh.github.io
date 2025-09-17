---
layout: post
title: Cisco ASA Understanding the show blocks command
date: 2015-11-11 21:58
comments: true
categories:
- cisco
- asa
- blocks
- troubleshooting
---
The `show blocks` command is a handy command for checking the memory usage on a Cisco PIX or ASA. But what does the output mean?

```
PrimaryASA# sh blocks
  SIZE    MAX    LOW    CNT
      4   1600  1579   1599
     80    400   357    400
     56   1012   964   1012
   1550   2453     0   1682
```
 
Each column has a different meaning. 


### SIZE column
This column defines where that memory is used within the firewall.

* `4` memory reserved for certain traffic like DNS, IKE, TFTP (Traffic that is small and bursty)
* `80` used to store failover hello’s and TCP intercept acks
* `256` more stateful failover messages
* `1550` memory used to process for Ethernet (10M and 100M) packets as they pass through the firewall
* `16384` memory used for gigabit Ethernet

### MAX column

Shows the max amount of memory available for that function.

### LOW column

Shows the lowest number of blocks that have been available since firewall booted or blocks were cleared.

### CNT column

The available number of blocks right now.

 
## Troubleshooting
Use `clear blocks` to reset the LOW and CNT values.

The following syslog will appear if the ASA starts running low on free memory.

```
asa-3-321007: System is low on free memory blocks of size 1550 (10 CNT out of 7196 MAX)
```

You can see from the output above that memory block 1550 is what is being utilized heavily. This is the gigabit ethernet memory store. Try to find what interface is triggering this high usage with `show blocks interface`.

![ASA show blocks IMG](/images/showblocks.png)
	
	
