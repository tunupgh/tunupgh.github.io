---
layout: post
title: Using the Windows Command line like a pro
date: 2013-06-18 19:17
comments: true
categories:
- windows
- win7
- tips
- misc
---
In this post I will be answering such question as: What is the 'cat' equivalent in Windows? What is the 'grep' equivalent in Windows? Yes that's right, we are visiting the Windows CLI today. The old DOS shell.

These commands all work in Windows 7 and Server 2008; most of them also work in Windows XP. 

## Using the Type+Find+Sort combo
The windows command `type` is similar to the `cat` command in linux. Then you can pipe it to `find` which is like `grep`. Then you can `sort` it to your liking. This command combination can be a very powerful tool for productivity.

#### To display all of the lines in hosts.txt file that contain the text "dmz" in any combination of upper or lower case (`/i` is to "ignore case"):
S:\network> `type hosts.txt | find /i "dmz"`

#### Look at all of the lines in hosts.txt file that have "mex" in them, sorted on the 20th character
S:\network> `type hosts.txt | find /i "mex" | sort /+20`

#### Get all of the lines that have the term "mex" in it and ignore the case.
S:\network> `type hosts.txt | find /i "mex"`<br>
25.25.25.251        MEXSR01            # Exchange server<br>
192.168.1.21        MEXPS01            # Power Supply<br>
192.168.1.22        MEXSW02            # Network Switch<br>
25.25.25.250        MEXRT02            # Router on second floor<br>

#### Gets all of the lines with "mex" in it, but eliminate the lines mentioning "PS":
S:\network> `type hosts.txt | find /i "mex" | find /i /v "ps"`<br>
25.25.25.251        MEXSR01            # Exchange server<br>
192.168.1.22        MEXSW02            # Network Switch<br>
25.25.25.250        MEXRT02            # Router on second floor<br>

#### Gets all lines with "192.168" and "mex".
S:\network> `type hosts.txt | find /i "192.168" | find /i "mex"`<br>
192.168.1.21        MEXPS01            # Power Supply<br>
192.168.1.22        MEXSW02            # Network Switch<br>

#### Run through all of the text files in the current directory, and gets lines containing notconnect.
T:\configs> `type *.txt | find /i "notconnect"`<br>
tokyo.txt<br>
Gi1/9        unused-july18      notconnect   1            auto   auto 10/100/1000BaseT

moskow.txt<br>
Gi1/9        unused-july18      notconnect   1            auto   auto 10/100/1000BaseT


## Starting Programs

### Using the start command

#### Open Windows Explorer in the current directory
T:\configs> `start .`

#### Open Firefox to Google search page with Google Instant disabled:
d:\\> `start firefox http://google.com/webhp?complete=0`

#### To open a specific .doc file, you can just use "start" this way, from a command line (Window will use the default program associated with .doc files):
C:\\> `start t:\configs\daily-update.doc`

### Useful shortcuts for frequently used programs

#### Open the Remote Desktop console connection to server 192.168.1.1
d:\\> `mstsc /console /v:192.168.1.1`

#### Create a Windows Firewall rule that allows a server to reply to ping requests
D:\\> `netsh advfirewall firewall add rule name="allow-ping-replies" protocol=icmpv4:8,any dir=in action=allow
Ok.`

#### Open directly to Device Manager
d:\\> `devmgmt.msc`

#### Open directly to the Windows Firewall with Advanced Security console
d:\\> `wf.msc`

#### Open directly to Disk Management
d:\\> `diskmgmt.msc`

#### Open directly to the System Properties applet
d:\\> `sysdm.cpl`

#### Open the services
c:\\> `services.msc`

#### Open the boot and system config
c:\\> `msconfig`

## Networking commands

#### Display the IP addresses of each NIC
d:\\> `ipconfig`

#### Show the MAC addresses of each NIC
d:\\> `getmac`

#### Show the routing table
d:\\> `route print`

#### List TCP/IP stats and information
d:\\> `netstat`

#### Open the Network Connections configurator (network control panel applet)
d:\\> `ncpa.cpl`

#### 

## Other useful commands

#### Initiates an immediate, forced reboot (-r is "restart", -f is force, -t 0 is in 0 seconds) with a comment (-c) to be recorded in the Event Viewer
d:\\> `shutdown -r -f -t 0 c Services are hung; rebooting system`

#### To map the letter T: to a Windows share on a file server:
d:\\> `net use t: \\example.com\common\is\euc`

#### Run the systeminfo command, and displays only the line referring to system boot time:
d:\\> `systeminfo | find /i "boot time"`<br>
System Boot Time:          6/4/2013, 12:53:27 PM
