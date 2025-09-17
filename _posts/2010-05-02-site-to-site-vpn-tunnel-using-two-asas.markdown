---
layout: post
title: Site to site VPN between two ASAs
categories:
- scripts
- asa
- cisco
- l2l
- script
- site to site
- tunnel
- vpn
comments: true
---
![ASA site to site diagram - IMG](/images/asasitetosite.jpg)

The classic site to site VPN tunnel between two ASAs. This configuration script is for ASA versions 8.2.5 and below. The new version has next gen encryption and has different keywords.

After applying the config below the device at 192.168.11.2 should be able to access 172.16.22.2 and vice versa.

### BLUE ASA

```
!^^^^^^^ ISAKMP (Phase 1) ^^^^^^^!
! must match with the other side in order for Phase 1 to complete.
! Lower policy numbers will likely be used before higher ones.
crypto isakmp policy 5
  authentication pre-share
  encryption aes
  hash sha
  group 2
  lifetime 86400

! Enable ISAKMP on the outside interface crypto isakmp enable OUTSIDE 
! Define the pre-shared-key 
tunnel-group 22.22.22.22 type ipsec-l2l 
tunnel-group 22.22.22.22 ipsec-attributes 
  pre-shared-key sekretk3y

!^^^^^^^ IPSEC (Phase 2) ^^^^^^^! 
! Define the interesting traffic in the ACL 
access-list ACL-RED-VPN permit ip 192.168.11.0 255.255.255.0 172.16.22.0 255.255.255.0 
crypto ipsec transform-set ESP-AES128-SHA esp-aes esp-sha-hmac 

! Create a crypto map entry that defines the tunnel 
crypto map MAP-OUTSIDE 20 set peer 22.22.22.22 

! ACL must be exactly the opposite of the other sides ACL 
crypto map MAP-OUTSIDE 20 match address ACL-RED-VPN 

! Transform set must match other side identically 
crypto map MAP-OUTSIDE 20 set transform-set ESP-AES128-SHA 
crypto map MAP-OUTSIDE 20 set security-association lifetime kilobytes 10000 

! Apply crypto map to an interface 
crypto map MAP-OUTSIDE interface OUTSIDE

!^^^^^^^ Routes and No-NATS ^^^^^^^! 
! Point the destination network out the outside interface with a next hop as the default gateway. 
route OUTSIDE 172.16.22.0 255.255.255.0 11.11.11.1 

! Make sure that the VPN traffic is NOT NAT'd 
access-list ACL-INSIDE-NONAT extended permit ip 192.168.11.0 255.255.255.0 172.16.22.0 255.255.255.0 
nat (INSIDE) 0 access-list ACL-INSIDE-NONAT
```

### RED ASA

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
access-list ACL-BLUE-VPN permit ip 172.16.22.0 255.255.255.0 192.168.11.0 255.255.255.0
crypto ipsec transform-set ESP-AES128-SHA esp-aes esp-sha-hmac
crypto map MAP-VPN 10 match address ACL-BLUE-VPN
crypto map MAP-VPN 10 set peer 11.11.11.11
crypto map MAP-VPN 10 set transform-set ESP-AES128-SHA
crypto map MAP-VPN 10 set security-association lifetime kilobytes 10000
crypto map MAP-VPN interface OUTSIDE
!^^^^^^^ Routes and No-NATS ^^^^^^^!
route OUTSIDE 192.168.11.0 255.255.255.0 22.22.22.1
access-list ACL-INSIDE-NONAT extended permit ip 1172.16.22.0 255.255.255.0 192.168.11.0 255.255.255.0
nat (INSIDE) 0 access-list ACL-INSIDE-NONAT
```