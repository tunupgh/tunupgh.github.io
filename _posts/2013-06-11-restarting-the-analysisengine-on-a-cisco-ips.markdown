---
layout: post
title: Restarting the AnalysisEngine on a Cisco IPS
date: 2013-06-11 19:55
comments: true
categories:
- cisco
- troubleshooting
- ips
- ids
- analysisengine
- intrusion prevention
---
Sometimes the analysis engine goes down on a Cisco IPS (Intrusion Prevention System) or IDS (Intrusion Detection System). In that case the analysis engine can be restarted from the service account by following these steps.

**Caution: If you restart the Analysis Engine in an IPS Module which is in a firewall which is acting as the active firewall, this will cause a firewall failover to occur.**

### What's wrong?
To determine if the analysis engine is stopped, type `show version`.

```
charlotte-ipsm# sh ver
Application Partition:
Cisco Intrusion Prevention System, Version 7.1(7)E4
Host:                                                 
    Realm Keys          key1.0                        
Signature Definition:                                 
    Signature Update    S722.0            2013-06-05  
OS Version:             2.6.29.1                      
Platform:               ASA-SSM-10                    
Serial Number:          JJJJJJJJJB                   
Licensed, expires:      01-Aug-2013 UTC               
Sensor up-time is 91 days.
Using 666M out of 974M bytes of available memory (68% usage)
system is using 29.0M out of 160.0M bytes of available disk space (18% usage)
application-data is using 65.1M out of 169.5M bytes of available disk space (40% usage)
boot is using 54.7M out of 69.7M bytes of available disk space (83% usage)
application-log is using 123.5M out of 513.0M bytes of available disk space (24% usage)

MainApp            S-2013_FEB_05_05_37_7_1_6_65   (Release)   2013-02-05T05:40:22-0600   Running  
AnalysisEngine     S-2013_FEB_05_05_37_7_1_6_65   (Release)   2013-02-05T05:40:22-0600   NotRunning  
CollaborationApp   S-2013_FEB_05_05_37_7_1_6_65   (Release)   2013-02-05T05:40:22-0600   Running  
CLI                S-2013_FEB_05_05_37_7_1_6_65   (Release)   2013-02-05T05:40:22-0600            
```
Notice the `NotRunning` status? When this is not running it won't do inspection of the IPSM. Restarting this is important to having a working IPS.

If this happens to a IPS Module in a firewall it will not cause a failover to occur. However, restarting the service will.


### Login using a `service` account
A service account is different than a admin account. With this type of account you will be able to navigate around the Linux OS.

```
charlotte-ipsm# show users all
    CLI ID   User      Privilege      
*   26711    cisco     administrator  
             service   service 
```

SSH into the IPSM using the service account:

`ssh service@charlotte-ipsm`

### Restarting the CIDS service
Once you are ssh'd in, switch your user to root.

`su`

Use the same password you used to get in with your service account. Now stop the cids service.

`cd /etc/init.d`

`./cids stop`

The 'cids' service is the Cisco IDS service. Once the service stopped you can confirm it has stopped by issuing the following command:

`ps -ef | grep cids`

You may see your own grep for 'cids' but as long as there aren't more than 2 or so results it should be stopped.
 
Now restart the service.

`./cids start`

Exit out of the service ssh session.

### Verifying it's Working
Log back in as a normal admin user again. Issue your show version again (note this may take a few minutes for the engine to start all the way up).

```
charlotte-ipsm# sh ver
Application Partition:
Cisco Intrusion Prevention System, Version 7.1(7)E4
Host:                                                 
    Realm Keys          key1.0                        
Signature Definition:                                 
    Signature Update    S722.0            2013-06-05  
OS Version:             2.6.29.1                      
Platform:               ASA-SSM-10                    
Serial Number:          JJJJJJJJJB                   
Licensed, expires:      01-Aug-2013 UTC               
Sensor up-time is 91 days.
Using 666M out of 974M bytes of available memory (68% usage)
system is using 29.0M out of 160.0M bytes of available disk space (18% usage)
application-data is using 65.1M out of 169.5M bytes of available disk space (40% usage)
boot is using 54.7M out of 69.7M bytes of available disk space (83% usage)
application-log is using 123.5M out of 513.0M bytes of available disk space (24% usage)

MainApp            S-2013_FEB_05_05_37_7_1_6_65   (Release)   2013-02-05T05:40:22-0600   Running  
AnalysisEngine     S-2013_FEB_05_05_37_7_1_6_65   (Release)   2013-02-05T05:40:22-0600   Running  
CollaborationApp   S-2013_FEB_05_05_37_7_1_6_65   (Release)   2013-02-05T05:40:22-0600   Running  
CLI                S-2013_FEB_05_05_37_7_1_6_65   (Release)   2013-02-05T05:40:22-0600            
```
You can also monitor for real time IPS events from the command line by watching the output from this command:

`show event alert`

If you are seeing alerts from this output the system is back up and running as expected. It's possible that no alerts are being triggered due to the nature of the traffic. In that case you can verify the system is good by doing `show statistics analysis-engine` and watching the packets processed number to make sure it is going up.
