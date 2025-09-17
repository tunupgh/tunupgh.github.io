---
layout: post
title: Cisco MARS SIEM expert password
date: 2015-09-06 11:58
comments: true
categories:
- cisco
- scripts
- mars
---
The Cisco MARS SIEM devices are built on a linux OS. It's possible to get to that linux shell but Cisco tries to keep that method private. I've learned over time that most MARS devices have theh same expert password which allows access to the root shell.

Expert password: `x3*Gd=y/Dq98`

Here is an example on how to use the password.

```
[linuxbox ~]$  ssh pnadmin@10.10.10.10
pnadmin@10.10.10.10's password:
Last login: Sun Aug 30 10:28:03 2015 from 10.10.10.200

  CS MARS - Mitigation and Response System

    ? for list of commands

[pnadmin]$ expert
Password: x3*Gd=y/Dq98
[root@mars-lc01 bin]# pwd
/opt/janus/release/bin
[root@mars-lc01 bin]#
```