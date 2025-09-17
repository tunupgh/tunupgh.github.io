---
layout: post
title: Networking commands for the VMWare ESXi host command line
date: 2014-12-26 20:17
comments: true
categories:
- misc
- esxi
- vmware
---
If you have ssh access to a VMWare ESXi server these commands can help you navigate the different networking settings on the server.

You can use the `esxcli network ip` command to do a number of different things include listing and editing the interfaces, routes and dns servers.

```
~ # esxcli network ip 
Usage: esxcli network ip {cmd} [cmd options]

Available Namespaces:
  dns                   Operations pertaining to Domain Name Server configuration.
  interface             Operations having to do with the creation, management and deletion of VMkernel network interfaces (vmknic).
  ipsec                 Operations on Internet Protocol Security
  route                 Operations pertaining to route configuration
  connection            List active tcpip connections
  neighbor              Operations that can be performed on arp tables
```

### Show interface details

This command is helpful for seeing if the interface is enabled and what the MAC address is. 

```
~ # esxcli network ip interface list
vmk0
   Name: vmk0
   MAC Address: 6c:62:6d:a3:2e:c2
   Enabled: true
   Portset: vSwitch0
   Portgroup: Management Network
   VDS Name: N/A
   VDS UUID: N/A
   VDS Port: N/A
   VDS Connection: -1
   MTU: 1500
   TSO MSS: 65535
   Port ID: 33554436
```

This command will show the IP address of each interface and the subnet mask.

```
~ # esxcli network ip interface ipv4 get
Name  IPv4 Address    IPv4 Netmask   IPv4 Broadcast  Address Type  DHCP DNS
----  --------------  -------------  --------------  ------------  --------
vmk0  172.16.0.10     255.255.255.0  172.16.0.255    STATIC           false
```

The command below will show the physical status of the interface including if the link is up, the MAC address, and speed of the interface.

```
~ # esxcfg-nics -l
Name    PCI           Driver      Link Speed     Duplex MAC Address       MTU    Description                   
vmnic0  0000:06:00.00 r8168       Up   100Mbps   Full   6c:62:6d:a3:2e:c2 1500   Realtek Realtek 8168 Gigabit Ethernet
```

### Show the ARP table
This command will show the neighbors directly connected to the VMWare ESXi host. In other words this is the way to see the arp table.

```
~ # esxcli network ip neighbor list
Neighbor                   Mac Address        Vmknic    Expiry  State    
-------------------------  -----------------  ------  --------  ---------
172.16.0.2                 00:1f:ca:88:96:8c  vmk0    1139 sec           
fe80::6e62:6dff:fea3:2ec2  6c:62:6d:a3:2e:c2  vmk0       0 sec  Reachable
```


### Show the Routing Details
This command will show the routing table on the ESXi server.

```
~ # esxcli network ip route ipv4 list
Network       Netmask        Gateway       Interface  Source
------------  -------------  ------------  ---------  ------
default       0.0.0.0        172.16.200.2  vmk0       MANUAL
172.16.200.0  255.255.255.0  0.0.0.0       vmk0       MANUAL
```

This command will show just the default gateway.

```
~ # esxcfg-route
VMkernel default gateway is 172.16.200.2
```

To make routing changes on the command line use the `esxcli network ip route ipv4 add` command.
