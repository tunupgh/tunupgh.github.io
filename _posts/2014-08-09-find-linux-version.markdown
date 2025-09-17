---
layout: post
title: Find Linux Version
date: 2014-08-09 21:37
comments: true
categories:
- linux
- misc
---
Are you in a linux/unix machine and curious what flavor or distro of OS it is? There isn't one simple way to get this information since many OSs have different ways to find this. Here are some commands to help you find the linux version you're on.

## uname
Try the uname command:

`uname  -a`

	Linux local.hostname 3.2.0-54-generic #82-Ubuntu SMP i686 i686 i386 GNU/Linux

The first word in this output is helpful because it will at least tell you what OS you're running. Values here could be linux, HP-UX, FreeBSD, SunOS. This command also indicates whether the system is 64 bit or not. In the output above it's not 64 bit because it doesn't say x86_64 or something similar. 


## lsb_release
LSB stands for Linux Standard Base. Some flavors will have this command to help identify which Linux you're running. 

`lsb_release -a`

	No LSB modules are available.
	Distributor ID:	Debian
	Description:	Debian GNU/Linux 6.0.6 (squeeze)
	Release:	6.0.6
	Codename:	squeeze




## /etc/*release*
Sometimes the linux distro will indicate the distro and version the OS is on in files called /etc/**-release. Take a look at those files if you have them to see if they help find information you're looking for.

`cat /etc/*release`

	NAME="Ubuntu"
	VERSION="12.04.3 LTS, Precise Pangolin"

This will work for [any of the following Linux distributions](http://linuxmafia.com/faq/Admin/release-files.html).



## issue.net
Sometimes the version will be placed in the issue.net file. 

`cat /etc/issue.net`
	
	Ubuntu 12.04.3 LTS \n \l


## Debian versions
If the OS you are on is a debian derivative you can check the debian version by looking at this file.

`cat /etc/debian_version`

	wheezy/sid