---
layout: post
title: Packet Captures on Cisco ASA
date: 2013-06-06 14:23
comments: true
categories:
- cisco
- asa
- packet
- capture
- troubleshooting
---
One of my favorite troubleshooting tools on the Cisco ASA firewall is doing a packet capture. An incoming packet will hit the capture before any ACL or NAT or other processing. An outgoing packet will hit a capture last before being put on the wire.


## Starting the Capture

To start a packet capture from the CLI execute the following command:

```
capture <Capture Name> interface <Interface> match tcp host <Source IP> host <Destination IP> eq <Port>
```
An example capture may look like this:

```
capture CAP1 int INSIDE match ip host 1.1.1.1 host 2.2.2.2
```
That will capture any traffic coming from 1.1.1.1 going to 2.2.2.2 as a destination on any port. Also, it will capture the opposite too. Traffic coming from 2.2.2.2 going to 1.1.1.1 will be captured.


## Viewing the Capture

There are two ways to view what you have captured.

### Getting the pcap file

You can download the pcap file to examine it in wireshark. There are two ways to get the pcap file off the ASA.

#### Download pcap file from ASDM
Use a web browser and go to to your firewall's IP with a specific URL:<br>
`https://192.168.1.1/admin/capture/CAP1/pcap`

#### Download pcap from CLI
It is also possible to move a file from the ASA to a FTP server using this command:<br>

`copy /pcap capture:CAP1 ftp://user:pass@192.168.1.1/CAP1.pcap`


### Viewing the output at the CLI
To see what has been captures issue the following command from the CLI:<br>
`show capture CAP1`

The capture output for a TCP flow follows this template:

`HH:MM:SS.ms [ether-hdr] src-addr.src-port dest-addr.dst-port: tcp-flags [header-check] [checksum-info] sequence-number ack-number tcp-window urgent-info tcp-options`

Let's look more closely into what the 'tcp-flags' can show us.

Here is an example TCP capture broken down.

User 1.1.1.1 is accessing the website located at 2.2.2.2.

 

<code>1: 15:01:45.052762 1.1.1.1.12869 > 2.2.2.2.80: <font color="red">**S**</font> 3624439037:3624439037(0) win 8192 <mss 1260,nop,wscale 8,nop,nop,sackOK></code><br>
The <font color="red">**S**</font> here indicates this is a SYN.

 

<code>2: 15:01:45.053403 2.2.2.2.80 > 1.1.1.1.12869: <font color="red">**S**</font> 285283040:285283040(0) <font color="red">**ack**</font> 3624439038 win 8192 <mss 1380,nop,wscale 8,nop,nop,sackOK></code><br>
This packet has both a <font color="red">**S**</font> (syn) and an <font color="red">**ack**</font>. Notice here the source of this packet is the webserver 2.2.2.2. To really tell who initiated this flow originally look at the ports. You see that the source IP is coming from port 80 and its going to port 12869. This tells us this is return traffic and the original request was really TO port 80.

<code>3: 15:01:45.054501 1.1.1.1.12869 > 2.2.2.2.80: . <font color="red">**ack**</font> 285283041 win 260</code><br>
Here is the <font color="red">**ack**</font>. This signifies the completion of the 3 way handshake. If you see this in the capture you know that communication is taking place properly.

<code>4: 15:01:45.054852 1.1.1.1.12869 > 2.2.2.2.80: <font color="red">**P**</font> 3624439038:3624439328(290) ack 285283041 win 260</code><br>
Now the requester is sending a <font color="red">**P**</font>ush. This means  show me the data!

<code>5: 15:01:45.244463 2.2.2.2.80 > 1.1.1.1.12869: . <font color="red">**ack**</font> 3624439328 win 260</code><br>
The next packet is another <font color="red">**ack**</font>. The webserver says ok, I got your push.
 
<code>6: 15:01:46.344296 2.2.2.2.80 > 1.1.1.1.12869: <font color="red">**.**</font> 285283041:285284301(1260) ack 3624439328 win 260</code><br>
<code>7: 15:01:46.344418 2.2.2.2.80 > 1.1.1.1.12869: <font color="red">**.**</font> 285284301:285285561(1260) ack 3624439328 win 260</code><br>
Look carefully here. The header check here is simply <font color="red">**.**</font> which indicates data being sent. And it makes sense that data is being sent from the webserver to the user.

 

## Clearing/Removing Captures

"Clearing" the capture refers to getting rid of the data in the capture. To do this, issue the following command:<br>
`clear capture CAP1`

"Removing" a capture means to delete its contents and the listener from the ASA. To do this, issue this command:<br>
`no capture CAP1`


## Packet Capture Creator

With a valid CCO login, you can use the [Cisco Packet Capture Config Generator](https://cway.cisco.com/tools/CaptureGenAndAnalyse/) tool.
