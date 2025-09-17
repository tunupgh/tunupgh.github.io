---
layout: post
title: Route Add Windows - A Guide to Windows Routing
date: 2016-04-25 08:41
comments: true
categories:
- windows
- misc
- routing
---
Sometimes you need to manually add, change, or remove a route on a Windows machine. Here is quick guide to help you accomplish these tasks.

First start by opening a CMD prompt by going to start then typing `cmd`.

These commands should work for Win XP, Win Vista, Win 98, Win NT, Win 2000, Win 2008, Win 2012, Win 7, Win 8, Win 10.

## Show the Current Routing Table

```
C:\>route print
===========================================================================
Interface List
 11...54 ee 75 5b e3 c9 ......Intel(R) Ethernet Connection (3) I218-LM
 13...5c e0 c5 7f e4 c8 ......Intel(R) Dual Band Wireless-AC 7265
===========================================================================

IPv4 Route Table
===========================================================================
Active Routes:
Network Destination        Netmask          Gateway       Interface  Metric
          0.0.0.0          0.0.0.0   192.168.177.1    192.168.177.30     10
        127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
     192.168.60.0    255.255.255.0         On-link      192.168.60.1    276
     192.168.60.1  255.255.255.255         On-link      192.168.60.1    276
   192.168.60.255  255.255.255.255         On-link      192.168.60.1    276
    192.168.177.0    255.255.255.0         On-link     192.168.177.1    276
    192.168.177.1  255.255.255.255         On-link     192.168.177.1    276
  192.168.177.255  255.255.255.255         On-link     192.168.177.1    276
===========================================================================
Persistent Routes:
  None

```

The default route is represented by A destination/netmask of `0.0.0.0`. If there isn't a route with a more specific destination and netmask, the default route is used.

Use this [subnet calculator](/subnet-calculator/) if you need help subnetting.

## Add a Static Route
To add a route to the routing table use the `route add` command. An example looks like this:

```
route add 10.0.0.0 mask 255.0.0.0 192.168.177.1
```

You can optionally add a `metric 2` to the end if you want to add a specific weight to the route. The routes with the lowest metric will take precedence over higher metrics. By default, static routes have a metric of 6.

You can also optionally add a `IF 2` to the end. This will force the route to use interface 2. You can see what interface numbers you have with the `route print` command.


```
Active Routes:
Network Destination        Netmask          Gateway       Interface  Metric
         10.0.0.0        255.0.0.0         On-link     172.16.177.30      6
```



## Remove a Static Route

To remove a route you must use the `route delete` command and the destination. 

```
route delete 10.0.0.0
```



## Change a Static Route
If you simply want to update a static route you can use the `route change` command.

```
route CHANGE 157.0.0.0 MASK 255.0.0.0 157.55.80.5 METRIC 2 IF 2
```



## Troubleshooting

If you get the following error:
`The requested operation requires elevation.`

To resolve this you will need administrator access to the system. Go to the start menu and when you type `cmd` right click on the cmd.exe program and click `Run As Administrator`. This opens a CMD prompt with more privileges.


For any further help you can use the built in help the route command provides. Simply type `route` and hit enter.

```
C:\>route

Manipulates network routing tables.

ROUTE [-f] [-p] [-4|-6] command [destination]
                  [MASK netmask]  [gateway] [METRIC metric]  [IF interface]

  -f           Clears the routing tables of all gateway entries.  If this is
               used in conjunction with one of the commands, the tables are
               cleared prior to running the command.

  -p           When used with the ADD command, makes a route persistent across
               boots of the system. By default, routes are not preserved
               when the system is restarted. Ignored for all other commands,
               which always affect the appropriate persistent routes. This
               option is not supported in Windows 95.

  -4           Force using IPv4.

  -6           Force using IPv6.

  command      One of these:
                 PRINT     Prints  a route
                 ADD       Adds    a route
                 DELETE    Deletes a route
                 CHANGE    Modifies an existing route
  destination  Specifies the host.
  MASK         Specifies that the next parameter is the 'netmask' value.
  netmask      Specifies a subnet mask value for this route entry.
               If not specified, it defaults to 255.255.255.255.
  gateway      Specifies gateway.
  interface    the interface number for the specified route.
  METRIC       specifies the metric, ie. cost for the destination.

All symbolic names used for destination are looked up in the network database
file NETWORKS. The symbolic names for gateway are looked up in the host name
database file HOSTS.

If the command is PRINT or DELETE. Destination or gateway can be a wildcard,
(wildcard is specified as a star '*'), or the gateway argument may be omitted.

If Dest contains a * or ?, it is treated as a shell pattern, and only
matching destination routes are printed. The '*' matches any string,
and '?' matches any one char. Examples: 157.*.1, 157.*, 127.*, *224*.

Pattern match is only allowed in PRINT command.
Diagnostic Notes:
    Invalid MASK generates an error, that is when (DEST & MASK) != DEST.
    Example> route ADD 157.0.0.0 MASK 155.0.0.0 157.55.80.1 IF 1
             The route addition failed: The specified mask parameter is invalid.
 (Destination & Mask) != Destination.

Examples:

    > route PRINT
    > route PRINT -4
    > route PRINT -6
    > route PRINT 157*          .... Only prints those matching 157*

    > route ADD 157.0.0.0 MASK 255.0.0.0  157.55.80.1 METRIC 3 IF 2
             destination^      ^mask      ^gateway     metric^    ^
                                                         Interface^
      If IF is not given, it tries to find the best interface for a given
      gateway.
    > route ADD 3ffe::/32 3ffe::1

    > route CHANGE 157.0.0.0 MASK 255.0.0.0 157.55.80.5 METRIC 2 IF 2

      CHANGE is used to modify gateway and/or metric only.

    > route DELETE 157.0.0.0
    > route DELETE 3ffe::/32

```

Check out this article for more [cool Windows CLI commands](/using-the-windows-command-line-like-a-pro/).
