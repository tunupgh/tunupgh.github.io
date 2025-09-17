---
layout: post
title: Cisco ASA command to show listening ports
date: 2016-09-29 15:05
comments: true
categories:
- cisco
- asa
- troubleshooting
---
Do you wish there was a `netstat` command for a Cisco ASA? Are you looking to see what ports the ASA has open and is listening on? Try this command:

```
ASA5506# sh asp table socket

Protocol  Socket    State      Local Address        Foreign Address
SSL       0001ebf8  LISTEN     172.16.3.1:443       0.0.0.0:*
TCP       000216f8  LISTEN     172.16.3.1:22        0.0.0.0:*
TCP       370c1d68  LISTEN     99.99.99.99:22       0.0.0.0:*
TCP       3712d678  ESTAB      172.16.3.1:22        172.16.2.3:50233
```

This command displays all of the ports that are open and established on the ASA. 

The `state` indicates whether the port is listening or established. 

The `local address` says which IP it's listening on. In the example above, 99.99.99.99 is the outside interface and the 172.16.3.1 is the inside interface. 

The `foreign address` will show up when someone connects to that port. In this case I was ssh'd into the firewall coming from 172.16.2.3.

The output above should be a direct reflection of the `ssh`, `telnet`, and `http` commands in the ASA.




