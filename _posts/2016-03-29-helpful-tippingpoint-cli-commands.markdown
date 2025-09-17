---
layout: post
title: Helpful TippingPoint CLI Commands
date: 2016-03-29 23:58
comments: true
categories:
- tippingpoint
- misc
---
Below are some helpful TippingPoint commands I use frequently.


### Rule-stats
If the TippingPoint is going into performance protection or throwing high CPU alarms this command is helpful. It will show you which rules (filters) are taking up a lot of resources. The ones that are top on the list should be checked to see if this is on by default or manually turned on. Disabling the filter could help performance.

```
nyc-tp# show np
nyc-tp(show-np)# rule-stats
  Filter      Flows  Success  % Total  % Success    Zoneless  % Zoneless
    3014   17997256        1        7       0.00           0           0
    2772   16133878        0        6       0.00           0           0
    2341   16133873        0        6       0.00           0           0
    2356   16133873        0        6       0.00           0           0
    2289   16133873        0        6       0.00           0           0
    2342   16133873        0        6       0.00           0           0
   10855   15738004        0        6       0.00           0           0
    4616   10306210        0        4       0.00           0           0
    5457   10306205        0        4       0.00           0           0
    5456   10306205        0        4       0.00           0           0
    6545   10306204      797        4       0.00           0           0
    6515   10306204        0        4       0.00           0           0
    2568    5929575        0        2       0.00           0           0
    5409    3275897        0        1       0.00           0           0
    4460    2681388        0        1       0.00           0           0
   4488    2010966        0        0       0.00           0           0
    3037    1863347        0        0       0.00           0           0
    8263    1810400        6        0       0.00           0           0
    8386    1770011        0        0       0.00           0           0
    4847    1770011        0        0       0.00           0           0
Total of 234867225 flows
```
 
### Sxoftlinx
Run softlinx a few times. Hope that the false trigger string is changing each time you run it. If it is the same every time then check the signature ID to get more info.


```
nyc-tp(show-np)# softlinx
Matched both softlinx and a rule:               673348
Matched softlinx, but not a rule:               1563846277
Matched a rule, but not softlinx:               2391595
Did not pass through the softlinx:              42380660

Highest single trigger match:                   681137330
Highest single trigger string:                  4A 50 47 3F 26  "JPG?&"
  Associated policies:
    00000002-0002-0002-0002-000000003885

Highest single false trigger (RTN):     80
Highest single false trigger string:            50 52 56 54 41 51 49 53  "PRVTAQIS"
  Associated policies:
    00000002-0002-0002-0002-000000005572
    00000002-0002-0002-0002-000000005573
```



### General stats

``` 
nyc-tp(show-np)# general statistics
General Statistics:
-------------------
Incoming                  =          629753382103
Outgoing                  =          629727486632
Congestion                =                117018
Drop by tcpreorder        =                     0
Second Tier               =            1540729682
Matched                   =               3064898
Blocked                   =              25769090
Permitted                 =                374636
Invalid                   =                  1906
```

### Tier-stats
Tier 1 = hardware
Tier 2 = buffer to send to tier 3 (should always be close to or at 100%)
Tier 3 = cpu and deep inspection

``` 
nyc-tp(show-np)# tier-stats
Tier 1:
  Receive  Mbps      = 45
  Transmit Mbps      = 45
  Max. receive Mbps  = 387
  Max. transmit Mbps = 387
  Receive  pkts/sec  = 10146
  Maximum  pkts/sec  = 106520
  Average pkt size   = 558
  Utilization        = 3 %
  Ratio to next tier = 40.77 %

Tier 2:
  Utilization        = 3 %
  Ratio to next tier = 100.00 %

Tier 3:
  Receive  Mbps      = 18
  Transmit Mbps      = 18
  Max. receive Mbps  = 216
  Max. transmit Mbps = 216
  Receive  pkts/sec  = 2106
  Maximum  pkts/sec  = 33058
  Average pkt size   = 1097
  Utilization        = 12 %
  Ratio to next tier = 0.30 %
```


 


