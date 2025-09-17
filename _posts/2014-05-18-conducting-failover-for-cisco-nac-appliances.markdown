---
layout: post
title: Conducting Failover for Cisco NAC appliances
date: 2014-05-18 18:43
comments: true
categories:
- cisco
- nac
- cas
- troubleshooting
- failover
---
When conducting a failover of a Cisco NAC cluster, first determine the status of the node you’re currently on:

```
. /perfigo/common/bin/fostate.sh
My node is active, peer node is standby
```

Next, conduct the failover by doing one of the following commands:
 
`shutdown`<br>
`reboot`<br>
`service perfigo stop`<br>

 

 
#### Source

[http://www.cisco.com/c/en/us/td/docs/security/nac/appliance/configuration_guide/416/CAM/416cam_book/m_ha.html#wp1047066](http://www.cisco.com/c/en/us/td/docs/security/nac/appliance/configuration_guide/416/CAM/416cam_book/m_ha.html#wp1047066)