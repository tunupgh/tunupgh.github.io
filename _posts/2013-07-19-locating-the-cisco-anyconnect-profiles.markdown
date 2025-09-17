---
layout: post
title: Locating the Cisco AnyConnect profiles
date: 2013-07-19 19:25
comments: true
categories:
- cisco
- anyconnect
- troubleshooting
- tips
---
## Local AnyConnect Profiles
XML and profile files are stored locally to the users machine. The location varies based on OS.

#### Windows XP
%ALLUSERSPROFILE%\Application Data\Cisco\
Cisco AnyConnect Secure Mobility Client\Profile

#### Windows Vista
%ProgramData%\Cisco\Cisco AnyConnect Secure Mobility Client\Profile

#### Windows 7
%ProgramData%\Cisco\Cisco AnyConnect Secure Mobility Client\Profile

#### Mac OS X
/opt/cisco/anyconnect/profile

#### Linux
/opt/cisco/anyconnect/profile
