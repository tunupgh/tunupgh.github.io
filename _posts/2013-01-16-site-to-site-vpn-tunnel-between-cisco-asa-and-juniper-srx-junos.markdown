---
layout: post
title: Site to Site VPN Tunnel Between Cisco ASA and Juniper SRX JunOS
categories:
- asa
- cisco
- firewall
- juniper
- junos
- scripts
- site to site
- srx
- vpn
comments: true
---
![ASA Site to site diagram - IMG](/images/asasitetosite.jpg)

Blue firewall: Juniper SRX 210 (JunOS 10.0R1.8)

Red firewall: Cisco ASA 5510 (OS 8.4)

This is a script to create a site to site VPN tunnel between a Cisco ASA and a Juniper SRX. The Juniper SRX will be using a policy based VPN.

### Blue Juniper SRX

```
# Create the IKE proposal
set security ike proposal IKE-SHA-AES128-DH1 authentication-method pre-shared-keys
set security ike proposal IKE-SHA-AES128-DH1 dh-group group2
set security ike proposal IKE-SHA-AES128-DH1 encryption-algorithm aes-128-cbc
set security ike proposal IKE-SHA-AES128-DH1 authentication algorithm sha1
set security ike proposal IKE-SHA-AES128-DH1 lifetime-seconds 86400

# Create the IKE policy
set security ike policy IKE-POLICY-HQ mode main
set security ike policy IKE-POLICY-HQ proposals IKE-SHA-AES128-DH1
set security ike policy IKE-POLICY-HQ pre-shared-key ascii-text s3kreTKey

# Create an IKE gateway
set security ike gateway IKE-GATEWAY-HQ ike-policy IKE-POLICY-HQ
set security ike gateway IKE-GATEWAY-HQ address 22.22.22.22
set security ike gateway IKE-GATEWAY-HQ external-interface ge-0/0/0.0

# Create an IPSec proposal/transform set
set security ipsec proposal IPSEC-SHA-AES128-ESP encryption-algorithm aes-128-cbc
set security ipsec proposal IPSEC-SHA-AES128-ESP authentication-algorithm hmac-sha1-96
set security ipsec proposal IPSEC-SHA-AES128-ESP protocol esp
set security ipsec proposal IPSEC-SHA-AES128-ESP lifetime-seconds 3600

# Create a IPSec policy
set security ipsec policy VPN-POLICY-HQ proposals IPSEC-SHA-AES128-ESP

# Create the IPSec VPN
set security ipsec vpn VPN-HQ ike gateway IKE-GATEWAY-HQ
set security ipsec vpn VPN-HQ ike ipsec-policy VPN-POLICY-HQ

# Add some networks into the address book
set security zones security-zone OUTSIDE address-book address NET-REMOTE-VPN 172.16.22.0/24
set security zones security-zone INSIDE address-book address NET-LOCAL 192.168.11.0/24

# Create the policies to define the interesting traffic
set security policies from-zone INSIDE to-zone OUTSIDE policy VPN-POLICY-HQ-OUT match source-address NET-LOCAL
set security policies from-zone INSIDE to-zone OUTSIDE policy VPN-POLICY-HQ-OUT match destination-address NET-REMOTE-VPN
set security policies from-zone INSIDE to-zone OUTSIDE policy VPN-POLICY-HQ-OUT match application any
set security policies from-zone INSIDE to-zone OUTSIDE policy VPN-POLICY-HQ-OUT then permit tunnel ipsec-vpn VPN-HQ
set security policies from-zone INSIDE to-zone OUTSIDE policy VPN-POLICY-HQ-OUT then permit tunnel pair-policy VPN-POLICY-HQ-IN

set security policies from-zone OUTSIDE to-zone INSIDE policy VPN-POLICY-HQ-IN match source-address NET-REMOTE-VPN
set security policies from-zone OUTSIDE to-zone INSIDE policy VPN-POLICY-HQ-IN match destination-address NET-LOCAL
set security policies from-zone OUTSIDE to-zone INSIDE policy VPN-POLICY-HQ-IN match application any
set security policies from-zone OUTSIDE to-zone INSIDE policy VPN-POLICY-HQ-IN then permit tunnel ipsec-vpn VPN-HQ
set security policies from-zone OUTSIDE to-zone INSIDE policy VPN-POLICY-HQ-IN then permit tunnel pair-policy VPN-POLICY-HQ-OUT

# You want to exclude the VPN traffic from being NAT'd
set security nat source rule-set NAT-INTERFACE rule NO-NAT match source-address 192.168.11.0/24
set security nat source rule-set NAT-INTERFACE rule NO-NAT match destination-address 172.16.22.0/24
set security nat source rule-set NAT-INTERFACE rule NO-NAT then source-nat off

# My config already had a rule in the rule-set. So I had to move the new rule above the old rule so it processes the no nat first.
insert source rule-set NAT-INTERFACE rule NO-NAT before rule RULE-NAME
```

### Red Cisco ASA Firewall

```
! Define the interesting traffic
access-list ACL-VPN-SRX extended permit ip 172.16.22.0 255.255.255.0 192.168.11.0 255.255.255.0

! Set the IKE parameters
crypto ikev1 enable OUTSIDE
crypto ikev1 policy 5
authentication pre-share
encryption aes
hash sha
group 2
lifetime 86400

! Create the IPSec settings
crypto ipsec ikev1 transform-set ESP-AES128-SHA esp-aes esp-sha-hmac
crypto map MAP-VPN 10 match address ACL-VPN-SRX
crypto map MAP-VPN 10 set peer 11.11.11.11
crypto map MAP-VPN 10 set ikev1 transform-set ESP-AES128-SHA
crypto map MAP-VPN interface OUTSIDE

! Create the tunnel group
tunnel-group 11.11.11.11 type ipsec-l2l
tunnel-group 11.11.11.11 ipsec-attributes
ikev1 pre-shared-key s3kreTKey

! In my case I needed to tell this VPN traffic to not be NAT'd as it goes through the firewall
object network OBJ-172.16.22.0
subnet 172.16.22.0 255.255.255.0
object network OBJ-192.168.11.0
subnet 192.168.11.0 255.255.255.0
nat (INSIDE,OUTSIDE) source static OBJ-172.16.22.0 OBJ-172.16.22.0 destination static OBJ-192.168.11.0 OBJ-192.168.11.0 no-proxy-arp description No NAT for VPN to SRX
```

There is a great tool to generate the Juniper SRX code on Juniper's site:

<a href="https://www.juniper.net/customers/support/configtools/vpnconfig.html">https://www.juniper.net/customers/support/configtools/vpnconfig.html</a>

To build the Juniper config I used this page as a reference guide:

<a href="http://www.juniper.net/techpubs/en_US/junos/topics/example/policy-based-vpn-using-j-series-srx-series-device-configuring.html">http://www.juniper.net/techpubs/en_US/junos/topics/example/policy-based-vpn-using-j-series-srx-series-device-configuring.html</a>
