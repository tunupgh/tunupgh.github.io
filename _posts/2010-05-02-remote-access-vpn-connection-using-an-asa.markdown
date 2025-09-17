---
layout: post
title: Remote access VPN connection using ASA
categories:
- scripts
- asa
- cisco
- ra
- remote access
- vpn
comments: true
---
![Remote Access Network Diagram - IMG](/images/ra-asa.jpg)

The blue firewall on the left is a Cisco ASA and the red computer on the right is any computer that is running the Cisco VPN Client.

After applying the config below the remote access user will be able to access the device at 192.168.11.2 as if it was on the same network as it. We will also be implementing a DNS entry so the Remote Access user can use hostnames.

### BLUE ASA5520 SCRIPT
<pre><code><span style="color: #808000;">! Create ACL's for NAT's and Split tunnel</span>
access-list ACL-RA-SPLIT standard permit host 192.168.11.2
access-list ACL-RA-SPLIT standard permit host 192.168.200.1
access-list ACL-INSIDE-NONAT extended permit ip host 192.168.11.2 10.100.6.0 255.255.255.248
access-list ACL-INSIDE-NONAT extended permit ip host 192.168.200.1 10.100.6.0 255.255.255.248
<span style="color: #808000;">! Create pool of IPs that will be assigned to the client.</span>
ip local pool POOL-RA-SPLIT 10.100.6.1-10.100.6.15 mask 255.255.255.248
<span style="color: #808000;">! Create the group policy to define this type of tunnel, dns, wins, domain information</span>
group-policy POLI-RA-SPLIT internal
group-policy POLI-RA-SPLIT attributes
  vpn-tunnel-protocol IPSec
  split-tunnel-policy   tunnelspecified
  split-tunnel-network-list value ACL-RA-SPLIT
  dns-server value 192.168.200.1
<span style="color: #808000;">! Create a tunnel-group to define the pre-shared key and pool</span>
tunnel-group TUN-RA-SPLIT type remote-access
tunnel-group TUN-RA-SPLIT general-attributes
  default-group-policy POLI-RA-SPLIT
  address-pool  POOL-RA-SPLIT
  tunnel-group TUN-RA-SPLIT ipsec-attributes
  pre-shared-key sekretK3Y
<span style="color: #808000;">! Define usernames for authentication
! If using radius, define the radius server with aaa-server commands and then in the tunnel-group authentication-server-group.</span>
username ra-user password passw0rd priv 1
<span style="color: #808000;">! Apply crypto statements</span>
crypto ipsec transform-set 3DES esp-3des esp-sha-hmac
crypto dynamic-map dynmap 10 set transform-set 3DES
crypto map Outside_Map 500 ipsec-isakmp dynamic dynmap
crypto map Outside_Map interface outside
crypto isakmp identity address
crypto isakmp enable outside
crypto isakmp policy 1
  authentication pre-share
  encryption 3des
  hash sha
  group 1
  lifetime 3600
crypto isakmp policy 2
</code></pre>

### Cisco VPN Client config

* Create a new VPN profile in the Cisco VPN Client
* Use the group name: TUN-RA-SPLIT.
* Use the username ra-user/passw0rd

![RA VPN Client - IMG](/images/ra-vpnclient.jpg)

![RA VPN Client - IMG](/images/ra-vpnclient2.jpg)
