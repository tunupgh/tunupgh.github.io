---
layout: post
title: How to edit iptables so it persists through reboot CentOS
date: 2017-06-11 23:13
comments: true
categories:
- linux
- misc
- iptables
---
Usually iptables are controlled by the **root** user or someone with **sudoer** privs.

You can check what iptables rules there are using:

`iptables -L -n`

That lists all the rules, and doesn't resolve port names or IP addresses.

To edit the rules, edit the following file:

`/etc/sysconfig/iptables`

Then you can reload those rules by doing the following:

`/etc/init.d/iptables restart`