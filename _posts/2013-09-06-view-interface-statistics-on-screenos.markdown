---
layout: post
title: View Interface Statistics on ScreenOS
date: 2013-09-06 11:11
comments: true
categories:
- juniper
- screenos
- interface
- counters
---
## Get command to see interface statistics on a SSG/ScreenOS

`get counter statistics interface <interface>`

This will display the following result:<br>

```
FLFW1-> get count stati int eth0/2
Hardware counters for interface ethernet0/2:
in bytes      2391954371 | out bytes     2188210410 | early frame            0
in packets    2536213416 | out packets    926090442 | late frame             0
in no buffer           0 | out no buffer          0 | re-xmt limit           0
in overrun             0 | out underrun           0 | drop vlan              0
in coll err            0 | out coll err           0 | out cs lost            0
in misc err            0 | out misc err           0 |                         
in dma err             0 | out bs pak             0 |                         
in crc err             0 | out discard            0 |                         
in align err           0 | out defer              0 |                         
in short frame         0 | out heartbeat          0 |                         

Hardware 64-bit counters for interface ethernet0/2:
in bytes                7278066553795 |  out bytes               1376577745130
in ucast                   6831180712 |  out ucast                  5221057738
in mcast                            0 |  out mcast                           0
in bcast                            0 |  out bcast                           0

Total flow counters for interface ethernet0/2:
in bytes      1363202958 | out bytes     2188210410 | tcp proxy              0
in packets    2521677948 | out packets    926090442 | tear drop            387
in vlan                0 | out vlan               0 | in permit     3912073647
out permit    1539534845 | src route              0 | no g-parent            0
ping of death          0 | no gate sess           0 | address spoof          0
in icmp         12232565 | no nat vector         11 | land attack            0
in self                0 | no map                 0 | icmp flood         77599
in un-auth             0 | no conn                0 | no arp entry           0
udp flood              0 | in unk prot            0 | no dip                 0
winnuke                0 | in vpn         222640182 | no gate                0
port scan            194 | in other               0 | no xmit vpnf           0
ip sweep               0 | no mac                 0 | no route               0
tcp out of seq     88121 | mac relearn            0 | no frag sess           0
wrong intf             0 | slow mac               0 | no frag netpak         0
wrong slot             0 | trmng queue            0 | no sa                734
icmp broadcast         0 | trmng drop             0 | no sa policy           0
illegal pak      4739684 | tiny frag              0 | sa inactive        52449
url block              2 | syn frag               0 | sa policy deny         0
encrypt fail           0 | connections            0 | policy deny            0
mp fail                0 | misc prot              0 | auth deny              0
auth fail              2 | loopback drop         16 | big bkstr              0
proc sess              0 | mal url                0 | sessn thresh           0
invalid zone           0 | null zone              0 | no nsp-tunnel          0
IP cls failure         0 | first pak frag        18 | unknown pak     14532135
multiauth drop         0 | multi-DIP drop         0 | tcp sweep              0
udp sweep              0 | tcp check drop         0 | 
```

## Deciphering Output

`address spoof`   Number of suspected address spoofing attack packets received<br>
`auth deny`   Number of times user authentication was denied<br>
`auth fail`   Number of times user authentication failed<br>
`big bkstr`   Number of packets that are too big to buffer in the ARP back store while waiting for MAC-to-IP address resolution<br>
`connections`   Number of sessions established since the last boot<br>
`encrypt fail`  Number of failed Point-to-Point Tunneling Protocol (PPTP) packets<br>
`*icmp broadcast`   Number of ICMP broadcasts received<br>
`icmp flood`  Number of ICMP packets that are counted toward the ICMP flood threshold<br>
`illegal pak`   Number of packets dropped because they do not conform to the protocol standards<br>
`in arp req`  Number of incoming arp request packets<br>
`in arp resp`   Number of outgoing arp request packets<br>
`in bytes`  Number of bytes received<br>
`in icmp`   Number of Internet Control Message Protocol (ICMP) packets received<br>
`in other`  Number of incoming packets that are of a different Ethernet type<br>
`in packets`  Number of packets received<br>
`in self`   Number of packets addressed to the Management IP address<br>
`*in un auth`   Number of unauthorized incoming TCP, UDP, and ICMP packets<br>
`*in unk prot`  Number of incoming packets using an unknown Ethernet protocol<br>
`in vlan`   Number of incoming vlan packets<br>
`in vpn`  Number of IPsec packets received<br>
`invalid zone`  Number of packets destined for an invalid security zone<br>
`ip sweep`  Number of packets received and discarded beyond the specified ip sweep threshold<br>
`land attack`   Number of suspected land attack packets received<br>
`loopback drop`   Number of packets dropped because they cannot be looped back through the security device. An example of a loopback session is when a host in the Trust zone sends traffic to a MIP or VIP address that is mapped to a server that is also in the Trust zone. The security device creates a loopback session that directs such traffic from the host to the MIP or VIP server. mac relearn Number of times that the MAC address learning table had to relearn the interface associated with a MAC address because the location of the MAC address changed<br>
`mac tbl full`  Number of times that the MAC address learning table completely filled up<br>
`mal url`   Number of blocked packets destined for a URL determined to be malicious<br>
`*misc prot`  Number of packets using a protocol other than TCP, UDP, or ICMP<br>
`mp fail`   Number of times a problem occurred when sending a PCI message between the master processor module and the processor module<br>
`no conn`   Number of packets dropped because of unavailable Network Address Translation (NAT) connections<br>
`no dip`  Number of packets dropped because of unavailable Dynamic IP (DIP) addresses<br>
`no frag netpak`  Number of times that the available space in the netpak buffer fell below 70%<br>
`*no frag sess`   The number of times that fragmented sessions were greater than half of the maximum number of NAT sessions<br>
`no g-parent`   Number of packets dropped because the parent connection could not be found<br>
`no gate`   Number of packets dropped because no gate was available<br>
`no gate sess`  Number of terminated sessions because there were no gates in the firewall for them<br>
`no map`  Number of packets dropped because there was no map to the trusted side<br>
`no nat vector`   Number of packets dropped because the Network Address Translation (NAT) connection was unavailable for the gate<br>
`*no nsp tunnel`  Number of dropped packets sent to a tunnel interface to which no VPN tunnel is bound<br>
`no route`  Number of unroutable packets received<br>
`no sa`   The number of packets dropped because no Security Associations (SA) was defined<br>
`no sa policy`  Number of packets dropped because no policy was associated with an SA<br>
`*no xmit vpnf`   Number of dropped VPN packets due to fragmentation<br>
`null zone`   Number of dropped packets erroneously sent to an interface bound to the Null zone<br>
`nvec err`  Number of packets dropped because of NAT vector error<br>
`out bytes`   Number of bytes sents<br>
`out defer`   Number of deferred outgoing packets<br>
`out packets`   Number of packets sent<br>
`out defer`   Number of deferred outgoing packets<br>
`out vlan`  Number of outgoing vlan packets<br>
`ping of death`   Number of suspected Ping of Death attack packets received<br>
`policy deny`   Number of packets denied by a defined policy<br>
`port scan`   Number of packets that are counted as a port scan attempt<br>
`proc sess`   Number of times that the total number of sessions on a processor module exceeded the maximum threshold<br>
`sa inactive`   Number of packets dropped because of an inactive SA<br>
`sa policy deny`  Number of packets denied by an SA policy<br>
`sessn thresh`  the threshold for the maximum number of sessions<br>
`*slow mac`   Number of frames whose MAC addresses were slow to resolve<br>
`src route`   Number of packets dropped because of the filter source route option<br>
`syn frag`  Number of dropped SYN packets because of a fragmentation<br>
`tcp out of seq`  Number of TCP segments received whose sequence number is outside the acceptable range<br>
`tcp proxy`   Number of packets dropped from using a TCP proxy such as the SYN flood protection option or user authentication<br>
`teardrop`  Number of packets blocked as part of a suspected Teardrop attack<br>
`tiny frag`   Number of tiny fragmented packets received<br>
`trmn drop`   Number of packets dropped by traffic management<br>
`trmng queue`   Number of packets waiting in the queue<br>
`udp hdlen err`   Number of packets where the IP header is less than the IP header + UDP header.  If IP Total Length is less than 28 bytes (8 bytes UDP header + 20 bytes IP header), it will increase this counter.  This type of traffic is not valid.<br>
`udp flood`   Number of UDP packets that are counted toward the UDP flood threshold<br>
`unknown pak`   Any packets with an Ethernet type that the firewall does not recognize. Examples would be Spanning Tree or proprietary Cisco protocols that the firewall does not pass or is capable of reading. When the firewall sees a packet with a Ethernet type it does not recognize, it will drop the packet and increment the 'unknown pak' counter.<br>
`url block`   Number of HTTP requests that were blocked<br>
`winnuke`   Number of WinNuke attack packets received<br>
`wrong intf`  Number of session creation messages sent from a processor module to the master processor module<br>
`wrong slot`  Number of packets erroneously sent to the wrong processor module<br>



## Sources
[http://kb.juniper.net/InfoCenter/index?page=content&id=KB4257](http://kb.juniper.net/InfoCenter/index?page=content&id=KB4257)<br>
[http://kb.juniper.net/InfoCenter/index?page=content&id=KB4261](http://kb.juniper.net/InfoCenter/index?page=content&id=KB4261)


