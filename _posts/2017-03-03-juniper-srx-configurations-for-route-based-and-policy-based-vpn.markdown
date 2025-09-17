---
layout: post
title: Juniper SRX Configurations for Route Based and Policy Based VPN
date: 2017-03-03 20:32
comments: true
categories:
- juniper
- vpn
- junos
- srx
---
There are two types site-to-site of VPNs on a Juniper SRX, policy based and route based. The policy based puts the traffic in a tunnel that is defined by a policy or ACL. The route based will put all traffic in the tunnel that is routed out a specific interface.

## Route Based VPN
In this configuration example, our peer is `22.22.22.22`. Both sides of the tunnel must be configured for route based VPN. To do this, we'll create an interface `st0` and route traffic into that interface. The SRX will tunnel all traffic going into that interface and send it over the VPN.

```
# Create the interface, add it to a zone, and route traffic to it
set interfaces st0 unit 0 family inet address 192.168.180.1/32
set security zones security-zone untrust interfaces st0.0 host-inbound-traffic system-services ike
set routing-options static route 172.16.100.0/24 next-hop st0.0

# Create the IKE proposal
set security ike propasal IKE-DH2-MD5-3DES authentication-method pre-shared-keys
set security ike propasal IKE-DH2-MD5-3DES dh-group group2
set security ike propasal IKE-DH2-MD5-3DES authentication-algorithm md5
set security ike propasal IKE-DH2-MD5-3DES encryption-algorithm 3des-cbc
set security ike propasal IKE-DH2-MD5-3DES lifetime-seconds 86400

# Create the IKE policy
set security ike policy IKE-POLICY-CHI mode main
set security ike policy IKE-POLICY-CHI proposals IKE-DH2-MD5-3DES
set security ike policy IKE-POLICY-CHI pre-shared-key ascii-text mysekr3tkey

# Create the IKE gateway
set security ike gateway IKE-GATEWAY-CHI ike-policy IKE-POLICY
set security ike gateway IKE-GATEWAY-CHI address 22.22.22.22
set security ike gateway IKE-GATEWAY-CHI dead-peer-detection interval 20
set security ike gateway IKE-GATEWAY-CHI dead-peer-detection threshold 5
set security ike gateway IKE-GATEWAY-CHI external-interface ge-0/0/1.0

# Create the IPSEC proposals
set security ipsec proposal IPSEC-ESP-MD5-3DES protocol esp
set security ipsec proposal IPSEC-ESP-MD5-3DES authentication-algorithm hmac-md5-96
set security ipsec proposal IPSEC-ESP-MD5-3DES encryption-algorithm 3des-cbc
set security ipsec proposal IPSEC-ESP-MD5-3DES lifetime-seconds 3200

# Create the IPSEC policy
set security ipsec policy IPSEC-POLICY-CHI perfect-forward-secrecy keys group2
set security ipsec policy IPSEC-POLICY-CHI proposals IPSEC-ESP-MD5-3DES

# Create the IPSEC VPN (notice the bind-interface st0.0)
set security ipsec vpn IPSEC-VPN-CHI bind-interface st0.0
set security ipsec vpn IPSEC-VPN-CHI ike gateway IKE-GATEWAY-CHI
set security ipsec vpn IPSEC-VPN-CHI ike ipsec-policy IKE-POLICY-CHI
set security ipsec vpn IPSEC-VPN-CHI establish-tunnels immediately

# If you have a PAT out the gateway interface then you want to exclude the VPN traffic from being NAT'd
set security nat source rule-set NAT-INTERFACE rule NO-NAT match source-address 192.168.11.0/24
set security nat source rule-set NAT-INTERFACE rule NO-NAT match destination-address 172.16.100.0/24
set security nat source rule-set NAT-INTERFACE rule NO-NAT then source-nat off
```



## Policy Based VPN
In this type of tunnel, we will define the traffic for the tunnel in a policy. Any traffic that matches the policy will attempt to go over the tunnel. We'll build this tunnel to our peer 22.22.22.22. This peer must also be configured for policy based VPN.

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

# If you have a PAT out the gateway interface then you want to exclude the VPN traffic from being NAT'd
set security nat source rule-set NAT-INTERFACE rule NO-NAT match source-address 192.168.11.0/24
set security nat source rule-set NAT-INTERFACE rule NO-NAT match destination-address 172.16.22.0/24
set security nat source rule-set NAT-INTERFACE rule NO-NAT then source-nat off

# My config already had a rule in the rule-set. So I had to move the new rule above the old rule so it processes the no nat first.
insert source rule-set NAT-INTERFACE rule NO-NAT before rule RULE-NAME
```








### Troubleshooting

Enable additional logging to see possibly why a VPN isn't forming:

```
set system syslog file kmd-logs daemon info
set system syslog file kmd-logs match KMD
```

Then do `show log kdm-logs` and you may be able to see at what part the tunnel is failing at.


**Traceoptions** is like Cisco's debug commands. You can enable this to see even more logs.

IKE Traceoptions

```
set security ike traceoptions flags ike
set security ike traceoptions file vpn-ike
```

Now do `show log vpn-ike` to see more information on the entire IKE process.

Show commands:

`show security ike associations`

`show security ipsec statistics`





### Additional Resources

Troubleshoot a VPN tunnel that is down with this guide: 

https://kb.juniper.net/InfoCenter/index?page=content&id=KB10100&actp=search

Here is a link to help you build the VPN configuration:

https://www.juniper.net/customers/support/configtools/vpnconfig.html

Reference guide for policy based VPNs:

http://www.juniper.net/techpubs/en_US/junos/topics/example/policy-based-vpn-using-j-series-srx-series-device-configuring.html