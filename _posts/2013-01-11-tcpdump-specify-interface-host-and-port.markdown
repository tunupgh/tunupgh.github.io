---
layout: post
title: TCPDump port, host, and interface
keywords: tcpdump port, tcpdump host, tcpdump interface
categories:
- misc
- linux
- tcpdump
- troubleshooting
comments: true
---
TCPDump is an extremely handy tool for verifying if packets are getting to the linux box or not. Here are the commands I use most often:

To specify which interface to listen on:

`tcpdump -i eth1`

To specify which IP address to listen for (will listen to both source and destination):

`tcpdump host 10.64.45.53`

To specify a port that is either source or destination:

`tcpdump port 8080`

To specify a source port use:

`tcpdump src port 8443`

To specify a destination port use:

`tcpdump dst port 514`

And of course you can add all of that together in one line using the "and" keyword:

`tcpdump -i eth1 host 10.64.45.53 and port 8080`