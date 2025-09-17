---
layout: post
title: VPN tunnel between an Ubuntu Linux machine and a Cisco ASA firewall
categories:
- scripts
- asa
- cisco
- linux
- ubuntu
- vpn
- vpnc
comments: true
---
### Config on the Cisco ASA (running 8.4) side. This config is identical to a normal remote access VPN:

```
! Create the general crypto statements
crypto ipsec ikev1 transform-set ESP-AES128-SHA esp-aes esp-sha-hmac 
crypto dynamic-map MAP-DYNAMIC 200 set ikev1 transform-set ESP-AES128-SHA
crypto map MAP-VPN 300 ipsec-isakmp dynamic MAP-DYNAMIC
crypto map MAP-VPN interface OUTSIDE
crypto ikev1 enable OUTSIDE
crypto ikev1 policy 5
 authentication pre-share
 encryption aes
 hash sha
 group 2
 lifetime 86400

! Define where the remote user can get to.
access-list ACL-RA-SPLIT standard permit host 172.16.200.200 

! Create the group policy
group-policy GRP-MYVPN internal
group-policy GRP-MYVPN attributes
 vpn-tunnel-protocol ikev1 
 split-tunnel-policy tunnelspecified
 split-tunnel-network-list value ACL-RA-SPLIT

! Create the tunnel group
tunnel-group TG-MYVPN type remote-access
tunnel-group TG-MYVPN general-attributes
 address-pool POOL-RA-SPLIT
 default-group-policy GRP-MYVPN
tunnel-group TG-KRONOLOGY ipsec-attributes
 ikev1 pre-shared-key suPerSeKret

! Create a username
username myUser password passw0rd privilege 1
```

### Config setup on the Ubuntu Linux side:

1. Download the Cisco VPN client

`sudo apt-get install vpnc`

2. Configure the VPN settings

`sudo vi /etc/vpnc/my-vpn.conf`

```
IPSec gateway 20.20.20.20
IPSec ID TG-MYVPN
IPSec secret suPerSeKret
Xauth username myUser
Xauth password passw0rd
```

starting vpn: `sudo vpnc-connect my-vpn`

stopping vpn: `sudo vpnc-disconnect`


This VPN configuration will time out every now and then and won't kick on again until you issue the above command to start it up again. In order to make this an always on VPN use a cron job. This example will check every 10 mins to see if tun0 exists if not, start connection):

```
sudo contab -e
*/10 * * * * [[ -d /sys/devices/virtual/net/tun0 ]] || /usr/sbin/vpnc /etc/vpnc/my-vpn.conf
```
