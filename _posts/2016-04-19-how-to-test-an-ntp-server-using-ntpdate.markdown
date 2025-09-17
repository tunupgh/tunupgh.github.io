---
layout: post
title: How to test an NTP server using ntpdate
date: 2016-04-19 21:29
comments: true
categories:
- misc
- ntp
---
Do you want to check if an NTP server is alive and responding to NTP queries? Do you need to manually check an NTP server to see if the clock is correct? Try using `ntpdate` from a linux CLI to query the NTP server.

```
$ ntpdate -q pool.ntp.org
server 64.71.128.26, stratum 2, offset 1.552116, delay 0.06792
server 104.236.236.188, stratum 2, offset 1.556884, delay 0.11574
server 108.59.2.24, stratum 2, offset 1.569006, delay 0.11952
server 209.114.111.1, stratum 2, offset 1.542965, delay 0.11389
19 Apr 21:30:06 ntpdate[32062]: step time server 64.71.128.26 offset 1.552116 sec
```

The `-q` switch says to query only and not set the time. With this command you can query an NTP server and it will return the date it has set.



