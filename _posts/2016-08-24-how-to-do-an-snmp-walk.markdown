---
layout: post
title: How to do an SNMP walk
date: 2016-08-24 15:53
comments: true
categories:
- misc
- snmp
---
Sometimes you need to manually SNMP poll a device. Here is how to do that.

## Do an entire pull

To conduct a full SNMP walk and get all OIDs, do the following from a linux command line:

```
snmpwalk -v 2c -c publicstring 192.168.1.1
```

In the example above we indicate the SNMP version (-v) is 2c, the community string (-c) is publicstring and the IP to SNMP poll is 192.168.1.1.

This will return all OIDs that are pollable on that system.

## Poll a single MIB

You can add a mib to the end of the snmpwalk command to poll a single mib and get only that result. Here is an example:

```
snmpwalk -v 2c -c publicstring 192.168.1.1 mib-2.47.1.1.1.1.5.7
```

You can also poll an attribute such as sysDescr like so:

```
snmpwalk -v 2c -c publicstring 192.168.1.1 mib-2.47.1.1.1.1.5.7
```









