---
layout: post
title: How to do a Continuous Ping
date: 2013-06-06 23:29
comments: true
categories:
- cisco
- scripts
- ping
- continuous
- windows
- osx
- unix
- linux
---
Below are methods to create a continuous ping in different environments.

To learn more about pings see the blog post [What is ping?](http://www.tunnelsup.com/what-is-ping/)

### Windows (CMD Prompt)
This works in Win95, Win98, WinXP, Win7, Win8 etc. Go to start, then run, type in cmd to open a command prompt.

`ping 192.168.1.1 -t`
<br><br>

### Unix/Linux Shell
In \*nix it is continuous by default. 

`ping 192.168.1.1`

You can also indicate a high count which will ping for a very long time. This example will ping something 9,999,999 times.

`ping 192.168.1.1 -c 9999999`

If you are in linux and it is not doing a continuous ping by default there is probably an alias for the ping command which is changing the way you envoke ping. Find where the ping command is using `which ping` then call the program from there and it should be continuous.

`/bin/ping 192.168.1.1`

<br><br>

### Mac and OSX
In OSX it is continuous by default. 

`ping 192.168.1.1`

If that doesn't work you can get it to be continuous by doing this command instead:

`/sbin/ping 192.168.1.1`
<br>

### Cisco Router/Firewall/Switch
You cannot do a continuous ping from a Cisco router, firewall or switch. This includes ASA and IOS operating systems. You can however do a ping for a VERY long time by indicating a lot of pings.

`ping 192.168.1.1 repeat 9999999`

Or if the repeat command is unavailable use the extended ping by simply issuing the `ping` command by itself and follow the prompts after.

If that still isn't good enough, you can set up an [SLA monitor](/cisco-asa-and-sla-monitoring/) to actually ping forever.
<br>

### Juniper JunOS
In JunOS it is continuous by default. 

`ping 192.168.1.1`
<br><br>

## Stopping a ping
On any of the systems above you can halt the ping.

To stop the continuous ping press `CONTROL`+`c`.
<br>
<hr>
Keywords: constant ping, continual ping, infinate ping, pertpetual ping, persistent ping