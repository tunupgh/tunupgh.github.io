---
layout: post
title: Starter Config for Cisco ASA 5506
date: 2016-04-16 22:46
comments: true
categories:
- cisco
- asa
- scripts
---
Are you trying to set up a Cisco ASA 5506 for the first time and want to see a sample config to get you started? Well then here's a good template to get started with. A 5506 is often intended for a small office or home office. In our scenario there is no DMZ and we are connecting to a cable modem using DHCP.

Here is a basic template you can use to get started with quickly. These commands will work with any ASA version 8.3 and up. I would say this will work with any ASA version 7.2 and up, except the NAT command and DHCP commands would not work.
```
!
hostname My-Firewall
enable password mysekretspecialenablepassw0rd
!
interface GigabitEthernet1/1
 nameif OUTSIDE
 security-level 0
 ip address dhcp setroute 
!
interface GigabitEthernet1/2
 nameif INSIDE
 security-level 100
 ip address 192.168.55.1 255.255.255.0 
!
interface GigabitEthernet1/3
 shutdown
 no nameif
 no security-level
 no ip address
!
interface GigabitEthernet1/4
 shutdown
 no nameif
 no security-level
 no ip address
!
interface GigabitEthernet1/5
 shutdown
 no nameif
 no security-level
 no ip address
!
interface GigabitEthernet1/6
 shutdown
 no nameif
 no security-level
 no ip address
!
interface GigabitEthernet1/7
 shutdown
 no nameif
 no security-level
 no ip address
!
interface GigabitEthernet1/8
 shutdown
 no nameif
 no security-level
 no ip address
!
interface Management1/1
 management-only
 no nameif
 no security-level
 no ip address
!
ftp mode passive
clock timezone PDT -7
object-group network OBJ-INSIDE-NETWORKS
 network-object 192.168.55.0 255.255.255.0
 network-object 192.168.56.0 255.255.255.0
access-list ACL-OUTSIDE-IN extended permit icmp any any echo-reply
access-list ACL-OUTSIDE-IN extended deny ip any any 
access-list ACL-INSIDE-IN extended permit ip object-group OBJ-INSIDE-NETWORKS any 
access-list ACL-INSIDE-IN extended deny ip any any 
pager lines 24
logging enable
logging timestamp
logging monitor notifications
logging buffered notifications
logging asdm informational
mtu OUTSIDE 1500
mtu INSIDE 1500
icmp unreachable rate-limit 1 burst-size 1
no asdm history enable
arp timeout 14400
no arp permit-nonconnected
nat (INSIDE,OUTSIDE) source dynamic any interface description Main PAT
access-group ACL-OUTSIDE-IN in interface OUTSIDE
access-group ACL-INSIDE-IN in interface INSIDE

route INSIDE 192.168.56.0 255.255.255.0 192.168.55.2 1
timeout xlate 3:00:00
timeout pat-xlate 0:00:30
timeout conn 1:00:00 half-closed 0:10:00 udp 0:02:00 icmp 0:00:02
timeout sunrpc 0:10:00 h323 0:05:00 h225 1:00:00 mgcp 0:05:00 mgcp-pat 0:05:00
timeout sip 0:30:00 sip_media 0:02:00 sip-invite 0:03:00 sip-disconnect 0:02:00
timeout sip-provisional-media 0:02:00 uauth 0:05:00 absolute
timeout tcp-proxy-reassembly 0:01:00
timeout floating-conn 0:00:00
user-identity default-domain LOCAL
aaa authentication ssh console LOCAL 
aaa authentication enable console LOCAL 
aaa authentication http console LOCAL 
no snmp-server location
no snmp-server contact
service sw-reset-button
crypto ipsec security-association pmtu-aging infinite
crypto ca trustpool policy
telnet timeout 5
no ssh stricthostkeycheck
ssh 0.0.0.0 0.0.0.0 INSIDE
ssh timeout 60
ssh key-exchange group dh-group1-sha1
console timeout 0
dhcpd auto_config OUTSIDE
!
dhcpd address 192.168.55.50-192.168.55.150 INSIDE
dhcpd enable INSIDE
!
threat-detection basic-threat
threat-detection statistics access-list
no threat-detection statistics tcp-intercept
dynamic-access-policy-record DfltAccessPolicy
username johndoe password oVIhyCAOOHIOur6g encrypted privilege 15
!
class-map inspection_default
 match default-inspection-traffic
!
!
policy-map type inspect dns preset_dns_map
 parameters
  message-length maximum client auto
  message-length maximum 512
policy-map global_policy
 class inspection_default
  inspect dns preset_dns_map 
  inspect ftp 
  inspect h323 h225 
  inspect h323 ras 
  inspect rsh 
  inspect rtsp 
  inspect esmtp 
  inspect sqlnet 
  inspect skinny  
  inspect sunrpc 
  inspect xdmcp 
  inspect sip  
  inspect netbios 
  inspect tftp 
  inspect ip-options 
!
service-policy global_policy global
prompt hostname context 
```

### Breakdown of commands

1. Set the interface configuration. In this template Gig1/1 is OUTSIDE and Gig1/2 is INSIDE. The OUTSIDE route is not only DHCP but I want it to be the default route. That's why the `setroute` command is there. The INSIDE interface has a static IP. Security level on the outside or untrusted network should always be higher than the inside or trusted network.

1. Create a NAT. In the template above there's only one PAT. This is important so all outgoing traffic can go through the firewall and get NAT'd to the outside IP of the firewall. 

1. Set any routes. In the example above we have the 192.168.56.0/24 network on the inside so we made a route for it. If there are no extra networks on the inside then we don't need this.

1. Set the ACLs. This is important to configure so it stops all incoming attacks to your network. The example above permits only ICMP (ping) replies and then denies everything else on the outside interface. Because a firewall is stateful, it will remember and permit any traffic originating from the inside and permit it back in. Essentially the template configuration let's you go out to the internet, but doesn't let any unwanted traffic in. I like to set the inside interface to only permit what I know exists inside my network. This is why the inside ACL permits only the inside networks.

1. Create a username and set the AAA configuration. In this case we created a johndoe username and set the `ssh` and `aaa` commands. This creates a user, configures logon settings, and permits ssh capabilities to the firewalls. You may want to lookup how to set up ssh for your ASA since you need to generate keys to do so.

1. Create a DHCP server (Optional). If you need to dynamically give DHCP addresses to hosts connecting to the ASA you can configure DHCP on the ASA to handle this. These commands are in the template. The `auto_config` command should pull any domain info and DNS servers out of the outside interface that uses DHCP to get it's information. If it doesn't then you can manually set the DNS server using `dhcpd dns 4.2.2.2`.







