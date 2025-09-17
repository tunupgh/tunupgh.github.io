---
layout: post
title: Site to Site VPN configuration script between PIX and ASA
categories:
- scripts
- '6.3'
- asa
- cisco
- l2l
- pix
- script
- vpn
comments: true
---
This script can be used to get you started on a site to site vpn using the older Cisco PIX code.

### PIX running 6.3

```
! ^^^^ Set ISAKMP (phase 1) parameters ^^^^^ ! 
isakmp enable outside
isakmp key sekretk3y address 22.22.22.22 netmask 255.255.255.255
isakmp policy 5 authentication pre-share
isakmp policy 5 encryption aes
isakmp policy 5 hash sha
isakmp policy 5 group 2
isakmp policy 5 lifetime 86400 

!^^^^ take care of interesting traffic ^^^^!
access-list ACL-VPN line 1 permit ip 192.168.100.0 255.255.255.0 192.168.200.0 255.255.255.0 
nat (inside) 0 access-list ACL-VPN

!^^^^ Set IPSEC (Phase 2) parameters ^^^^! 
crypto ipsec transform-set AES128-SHA esp-aes esp-sha-hmac crypto map MAP-VPN 10 ipsec-isakmp

crypto map MAP-VPN 10 match address ACL-VPN
crypto map MAP-VPN 10 set peer 22.22.22.22
crypto map MAP-VPN 10 set transform-set AES128-SHA
crypto map MAP-VPN interface outside
```

### ASA Configuration running (7.2 - 8.2.5 code)

```
!^^^^^^^ ISAKMP (Phase 1) ^^^^^^^!
crypto isakmp policy 15
  authentication pre-share
  encryption aes
  hash sha
  group 2
  lifetime 86400
crypto isakmp enable OUTSIDE tunnel-group 11.11.11.11 type ipsec-l2l
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