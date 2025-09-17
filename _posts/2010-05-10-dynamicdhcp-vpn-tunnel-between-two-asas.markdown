---
layout: post
title: Dynamic/DHCP VPN tunnel between two Cisco ASA's
categories:
- scripts
- cisco
- asa
- dhcp
- dynamic
- script
- vpn
comments: true
---
This script will create a vpn tunnel between one Cisco ASA that has a statically assigned IP and one Cisco ASA that has DHCP assigned IP which will change.

The caveat here is that the LAN with the DHCP side ASA needs to be the one that initiates the tunnel by sending interesting traffic. The Static side will not know which IP to peer with and therefor cannot bring this tunnel up, however, once the tunnel is up the static side can go across the tunnel because the peer is established.

### ASA WITH STATIC IP

```
! Create isakmp policy to negotiate on
crypto isakmp policy 5
  authentication pre-share
  encryption aes
  hash sha
  group 2
  lifetime 86400
crypto isakmp enable OUTSIDE

! The dynamic map tunnel group is DefaultL2LGroup. The pre-shared key needs to be defined within this tunnel group
tunnel-group DefaultL2LGroup ipsec-attributes
  pre-shared-key sekretk3y

crypto ipsec transform-set ESP-AES128-SHA esp-aes esp-sha-hmac
! Create  a dynamic-map
crypto dynamic-map MAP-DYN 20 set transform-set ESP-AES128-SHA
! Assign dynamic-map to crypto map
crypto map MAP-VPN 55 ipsec-isakmp dynamic MAP-DYN
crypto map MAP-VPN interface OUTSIDE

! create no-nat statements
access-list ACL-INSIDE-NONAT extended permit ip 192.168.100.0 255.255.255.0 192.168.200.0 255.255.255.0
nat (INSIDE) 0 access-list ACL-INSIDE-NONAT
```



### ASA WITH DYNAMIC/DHCP IP ADDRESS
Note: This config is exactly the same as a normal site to site VPN

```
!^^^^^^^ ISAKMP (Phase 1) ^^^^^^^!
crypto isakmp policy 15
 authentication pre-share
 encryption aes
 hash sha
 group 2
 lifetime 86400
crypto isakmp enable OUTSIDE
tunnel-group 11.11.11.11 type ipsec-l2l
tunnel-group 11.11.11.11 ipsec-attributes
 pre-shared-key sekretk3y

!^^^^^^^ IPSEC (Phase 2) ^^^^^^^!
access-list ACL-BLUE-VPN permit ip 192.168.200.0 255.255.255.0 192.168.100.0 255.255.255.0
crypto ipsec transform-set ESP-AES128-SHA esp-aes esp-sha-hmac
crypto map MAP-VPN 10 match address ACL-BLUE-VPN
crypto map MAP-VPN 10 set peer 11.11.11.11
crypto map MAP-VPN 10 set transform-set ESP-AES128-SHA
crypto map MAP-VPN 10 set security-association lifetime kilobytes 10000
crypto map MAP-VPN interface OUTSIDE

!^^^^^^^ Routes and No-NATS ^^^^^^^!
route OUTSIDE 192.168.100.0 255.255.255.0 22.22.22.1
access-list ACL-INSIDE-NONAT extended permit ip 192.168.200.0 255.255.255.0 192.168.100.0 255.255.255.0
nat (INSIDE) 0 access-list ACL-INSIDE-NONAT
```