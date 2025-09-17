---
layout: post
title: Delete IPv6 Translation methods for IPv6 in Windows OS
date: 2015-09-06 18:12
comments: true
categories:
- windows
- misc
---
The Windows operating system likes to turn on the IPV6 transition methods by default. There are some transition methods that don't work like 6to4, ISATPA, and Teredo. If you run ipconfig /all, you'll notice a large list of IPv6-related interfaces that are completely unnecessary and can be disabled. This won't disable IPv6, only the transition methods that aren't needed.

Open a command prompt in Windows and enter the following:
 
```
netsh interface 6to4 set state state=disabled undoonstop=disabled
netsh interface isatap set state state=disabled
netsh interface teredo set state type=disabled
```
