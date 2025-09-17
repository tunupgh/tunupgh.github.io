---
layout: post
title: Remote access VPN connection using Cisco Router
categories:
- cisco
- scripts
- asa
- l2l
- router
- script
- site to site
- vpn
comments: true
---
![Network Diagram - IMG](/images/ra-isr.jpg)

The blue router on the left is a Cisco router with VPN capabilities and the red computer on the right is any computer that is running the Cisco VPN Client.

After applying the config below the remote access user will be able to access the device at 192.168.11.2 as if it was on the same network as it. We will also be implementing a DNS entry so the Remote Access user can use hostnames.

### Blue Cisco Router Configuration

```
aaa authentication login VPNUSERSAUTH local
aaa authorization network VPNUSERS local
username ra-user password 0 passw0rd
crypto isakmp policy 7
encr 3des
hash md5
authentication pre-share
group 2
crypto isakmp client configuration group VPNUSERS
key sekretK3Y
pool VPN-POOL
acl ACL-SPLIT-VPN
crypto ipsec transform-set 3DES-SHA esp-3des esp-sha-hmac
crypto dynamic-map VPNDYNMAP 1
set transform-set ESP-AES128-SHA
reverse-route
crypto map MAP-OUTSIDE client authentication list VPNUSERSAUTH
crypto map MAP-OUTSIDE isakmp authorization list VPNUSERS
crypto map MAP-OUTSIDE client configuration address respond
crypto map MAP-OUTSIDE 6500 ipsec-isakmp dynamic VPNDYNMAP
ip local pool VPN-POOL 10.1.74.5 10.1.74.250
ip access-list extended ACL-SPLIT-VPN
permit ip 192.168.11.0 0.0.0.255 10.1.74.0 0.0.0.255
```

### Cisco VPN Client Configuration
Create a new VPN profile in the Cisco VPN Client

![VPN Client Config - IMG](/images/ra-vpnclient.jpg)

Now select the new profile and click connect. Use the username ra-user/passw0rd

![VPN Client Config - IMG](/images/ra-vpnclient.jpg)

