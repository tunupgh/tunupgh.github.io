---
layout: post
title: Cisco Nexus Packet Captures with Ethanalyzer
date: 2015-11-11 22:46
comments: true
categories:
- cisco
- ios
- troubleshooting
---
There is a lesser known built in packet capture tool in Nexus OS called Ethanalyzer.

Valid for Nexus models
7k, 6k, 5k, 3k, 1kv

[How to do packet captures on a Cisco ASA](/packet-captures-on-cisco-asa/)

[How to do packet capture on Cisco IOS Router](/packet-capture-for-cisco-ios-router/)

## Capturing packets on the data-plane

To capture specific IP flows.

Any traffic that is logged in an interface ACL can then be seen in the ethanalyzer.

```
ip access-list ACL-CAPTURE
permit ip 10.0.0.250/32 10.1.7.250/32 log
permit ip any any

int vlan 50
  ip access-group ACL-CAPTURE in

ethanalyzer local interface inband display-filter ip.src==10.0.0.250 limit-captured-frames 50
```

* Use [wireshark display-filters](http://packetlife.net/media/library/13/Wireshark_Display_Filters.pdf)! This will change what’s displayed.
* Use capture filters to change what is captured
* Command only available from the default VDC.

Use this command to create a pcap

```
ethanalyzer local interface inband write MYCAPTURE.pcap display-filter ip.src==10.0.0.250 limit-captured-frames 50
```
 
This will save the pcap file to the nexus which you can then use the `copy flash ftp` command to move it off the device.


 
### Limitation

It only runs in the default VDC. If you do not have access to the admin or default VDC you cannot use this command. You can still capture from another VDC by setting an interface ACL and log the traffic you want in the VDC you want. Then you can go to the default VDC and run ethanalyzer to see your traffic.

 
### References:

Using Ethanalyzer<br>
https://supportforums.cisco.com/docs/DOC-31148

Understanding wireshark relating to Cisco Catalyst and IOS devices<br>
http://www.cisco.com/en/US/docs/switches/lan/catalyst3850/software/release/3se/consolidated_guide/configuration_guide/b_consolidated_3850_3se_cg_chapter_01100110.html

Using the “monitor capture” command on IOS devices<br>
http://www.cisco.com/en/US/docs/ios-xml/ios/epc/command/monitor_capture_through_show_monitor_capture.html

 

 
