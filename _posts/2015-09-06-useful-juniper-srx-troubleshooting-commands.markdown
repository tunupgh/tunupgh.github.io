---
layout: post
title: Useful Juniper SRX Troubleshooting Commands
date: 2015-09-06 13:04
comments: true
categories:
- juniper
- srx
- junos
---
Here's a list of my favorite Juniper SRX Junos commands I use for troubleshooting.

### Show config as single lines instead of stanzas

```
edit
show | display set
```

This is useful for displaying the config to look more like the way a Cisco config looks. It's easier to search for keywords in the config with this command by doing things like `show | display set | match interface`. 

### Show the connections going through the SRX

```
user@SRX1> show security flow session
Session ID: 18999, Policy name: POL-INSIDE-TO-OUTSIDE/6, Timeout: 1632
  In: 172.16.200.43/37689 --> 199.199.199.199/443;tcp, If: ge-0/0/1.0
  Out: 199.199.199.199/443 --> 200.200.200.200/8915;tcp, If: ge-0/0/0.0
```

This will show detailed information of all the connections and flows going through the SRX. The output will look like this.

The output above displays a user on the inside going to a website on the outside. The user IP is `172.16.200.43`, the web server is `199.199.199.199`, and the SRX NAT'd this outbound flow to `200.200.200.200`.


### See uncommitted changes

```
edit
show | compare
```

When you want to make changes to an SRX it's best to check whether there are any uncommited changes before you begin making changes. This commands shows you what those are.


### Show previous commits

```
user@SRX-1> edit 
Entering configuration mode

[edit]
user@SRX-1# rollback ?
Possible completions:
  <[Enter]>            Execute this command
  0                    2015-06-15 20:23:16 UTC by zaphod via cli 
  1                    2015-05-19 23:40:21 UTC by arthur via cli 
  2                    2015-05-17 23:35:05 UTC by zaphod via cli 
  3                    2015-05-16 23:34:35 UTC by arthur via cli 
  4                    2015-05-11 23:33:20 UTC by ford via cli 
  5                    2015-05-10 23:31:48 UTC by ford via cli 
  |                    Pipe through a command
[edit]
```

The output above shows you the history of who did a change and when. This is why it's important that each user has their own username.


### See what was in a previous commit

```
edit
show | compare rollback 5
```

This will display the contents of rollback 5.


### Show CPU/Uptime/memory/temperature

```
show chassis routing-engine
```

Useful command to give a snapshot of multiple health statistics.

### Show front panel alarms

```
show chassis craft-interface
```

Some models of Juniper SRX have a craft interface. This command will show you the LED status of the front panel.



