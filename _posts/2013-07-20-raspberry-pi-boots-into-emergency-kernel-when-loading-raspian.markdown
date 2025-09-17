---
layout: post
title: Raspberry Pi boots into emergency kernel when loading raspian
date: 2013-07-20 15:36
comments: true
categories:
- electronics
- raspberry pi
- raspian
---
Recently I tried updating my Raspian OS on my Pi. After writing the OS to the card and popping it into the Pi, the Pi would only boot into the emergency kernel mode. Basically it dumped me into a very basic shell without the need to login or anything. It gives very limited commands and doesn't really allow for any real operation. Something definitely isn't right.

## The Fix
Edit the file `config.txt` and add this line into the bottom of the file:

```
avoid_safe_mode=1
```

This should now allow you to boot into regular kernel mode.

## The Reason for the Problem

The problem seems to be related to early Revision 1 model B boards. My board in particular shows this:

```
pi@raspberrypi:~$ grep Rev /proc/cpuinfo
Revision : 0002
```
It looks like it's Rev 2, but that output actually is saying Rev 1.

What's likely happening here is that I have a Rev 2 board but for some reason the OS thinks it's Rev 1. There are different pins that do different things on the two boards and therefore cause the wrong behavior to occur. 



