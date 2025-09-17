---
layout: post
title: How to set a static IP in linux
date: 2016-09-12 16:32
comments: true
categories:
- misc
- linux
---
The following will work for any debian base linux distro. That includes **Ubuntu**, **Kali**, **Raspberry Pi (Raspian Jessie)**, **TAILS**, and many more. 


## Edit Interface Settings

Edit the config:

```
sudo vi /etc/network/interfaces
```

Find the interface you wish to make static and add the following lines:

```
auto eth0
iface eth0 inet static
        address 192.168.1.100
        network 192.168.1.0
        netmask 255.255.255.0
        broadcast 192.168.1.255
        gateway 192.168.1.1
```

* `address` is the IP you want to assign this host
* `network` is the network portion of this subnet. It's usually the same as your static IP but ending in `.0`
* `netmask` is the netmask of your subnet. This is usually `255.255.255.255`
* `broadcast` is the broadcast IP of this subnet. It's usually the same as your static IP but ending in `.255`
* `gateway` is the next hop for this system to go. This is usually a router's IP and it must be in the same subnet as the host address.

## Configure DNS 

To resolve hostnames to IP addresses you must configure a DNS server. You can do this by editing the following:

```
vi /etc/resolv.conf
```

Add your DNS servers to this file or use Google's public DNS servers like this:

```
nameserver 4.2.2.2
nameserver 4.2.2.3
```

## Restart networking

To make these change effective you must restart the networking service. That can be done by doing one of the following:

```
sudo /etc/init.d/networking restart
```

If that doesn't work try:

```
sudo /etc/init.d/networking reload
```

If that doesn't work try:

```
sudo systemctl restart ifup@eth0
```

## Check settings

Now check if the settings took by running the following command:

```
ifconfig
```

The output looks like this:

```
eth0      Link encap:Ethernet  HWaddr d8:eb:97:bd:53:4e
          inet addr:192.168.1.100  Bcast:192.168.1.255  Mask:255.255.255.0
          inet6 addr: fe80::daeb:97ff:febd:534e/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:47436 errors:0 dropped:3117 overruns:0 frame:0
          TX packets:13017 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:5982679 (5.7 MiB)  TX bytes:1837349 (1.7 MiB)
```