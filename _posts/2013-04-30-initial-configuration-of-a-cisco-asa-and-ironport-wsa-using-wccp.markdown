---
layout: post
title: Initial configuration of a Cisco ASA and Ironport WSA using WCCP
categories:
- asa
- ironport
- scripts
- wccp
- web security appliance
- wsa
- cisco
comments: true
---
Today we are going to set up a Cisco ASA firewall to send WCCP (port 80) web inspection traffic to a Cisco Ironport WSA (Web Security Appliance).

Suppose the following:

Ironport WSA IP address: 192.168.5.55

Inside IP of ASA firewall: 192.168.5.1

Inside IP address range: 192.168.0.0/16

### Cisco ASA firewall configuration

```
! Define the Ironport IP Address in an ACL
access-list ACL-IRONPORT-WSA extended permit ip host 192.168.5.55 any

! Define what traffic should be inspected
access-list ACL-WEBPROXY-TRAFFIC extended permit tcp 192.168.0.0 255.255.0.0 any eq www

! Apply the WCCP configuration
wccp web-cache redirect-list ACL-WEBPROXY-TRAFFIC group-list ACL-IRONPORT-WSA
wccp interface INSIDE web-cache redirect in
```

### Ironport Configuration

Navigate to `Network` -> `Transparent Redirection`

Make the type WCCP v2 Router

Add a service with a name of `WEB_CACHE`, a router IP of `192.168.5.1` (ASA Inside IP) and port `80` (Standard).

At this point you can do a 'show wccp' on the ASA and you should see "Total Packets Redirected" rising. From within the Ironport go to `Reporting` -> `Overview` and you should see statistics of what traffic is now flowing through the Ironport.

### Troubleshooting techniques:
<ul>
	<li>Verify basic connectivity. Check interface IP addresses. Verify network access between both ASA and Ironport.</li>
	<li>Show commands on ASA: show wccp</li>
	<li>Debug commands on firewall: debug wccp packet, debug wccp events</li>
	<li>Verify the ports caught in the traffic ACL are the same as the ports used for WCCP and there's a listener (service) on Ironport to interpret them.</li>
</ul>
