---
layout: post
title: hosts file
date: 2015-01-22 20:47
comments: true
categories:
- misc
- windows
- mac
- linux
---
The 'hosts' file is a hostname to IP address map used for local DNS resolution. When a computer needs to talk to another computer it needs to first know the IP address of the second computer. This is where DNS converts a hostname to an IP address. 

## DNS Order of Operations 

When a computer needs to resolve a hostname it follows these steps.

1. Check if the host is the computer itself
1. Check the hosts file to see if there is an entry there
1. Do a DNS server query
1. If still not resolved, a Windows computer will do a local NetBIOS query to see if anyone on the local network has that hostname

By adding a host to the hosts file this will force the host to be resolved to the given IP instead of querying the DNS server. Some reasons to do this are:

* Creating a hosts entry for a hostname to match a SSL certificate value.
* Create a hosts entry to block access to a specific website. By mapping a hostname to 127.0.0.1, any request to that hostname won't leave the computer making the request.
* Create a hosts entry to make static DNS resolutions.  


## Syntax
No matter what OS you're using the syntax is the same for all of them. Edit the hosts file using any kind of text editor like notepad or vi. Make an entry like the following. Save it and immediately on saving the file the entries will be live.

```
93.184.216.34     example.com
172.16.2.2        mysite.com
```

This means that if the user tries to go to http://mysite.com they will be directed to 172.16.2.2.


## Location
The hosts file is in a different location depending on the OS.

#### Windows Hosts File

* Windows 8 `C:\Windows\System32\drivers\etc\`
* Windows 7	`C:\Windows\system32\drivers\etc\`
* Windows XP  `C:\Windows\system32\drivers\etc\`
* Windows NT  `C:\Windows\system32\drivers\etc\`
* Windows 95  `C:\Windows\`

#### Mac Hosts File

For OSX 10.2 and up the hosts file is found in `/etc/hosts`.

#### Linux Hosts File

In Unix and Linux operating systems the file is located in `/etc/hosts`.

