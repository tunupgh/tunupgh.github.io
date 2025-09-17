---
layout: post
title: Understanding Cisco ASA interface counters and statistics
date: 2013-07-29 13:00
comments: true
categories:
- cisco
- asa
- tips
- troubleshooting
- interface
- counters
- statistics
---
Upon doing a show interface command a lot of valuable information is displayed regarding the packets and errors on that interface.

```
USS-ASA/pri/act# sh int GigabitEthernet0/1
Interface GigabitEthernet0/1 "inside", is up, line protocol is up
  Hardware is i82546GB rev03, BW 1000 Mbps, DLY 10 usec
        Full-Duplex(Full-duplex), 100 Mbps(100 Mbps)
        Input flow control is unsupported, output flow control is off
        MAC address 442b.442b.442b, MTU 1500
        IP address 172.16.250.26, subnet mask 255.255.255.240
        16433456 packets input, 2581392514 bytes, 0 no buffer
        Received 111 broadcasts, 0 runts, 0 giants
        0 input errors, 0 CRC, 0 frame, 0 overrun, 0 ignored, 0 abort
        0 pause input, 0 resume input
        0 L2 decode drops
        24943232 packets output, 28662026144 bytes, 430 underruns
        0 pause output, 0 resume output
        0 output errors, 0 collisions, 0 interface resets
        0 late collisions, 0 deferred
        0 input reset drops, 0 output reset drops, 0 tx hangs
        input queue (blocks free curr/low): hardware (255/230)
        output queue (blocks free curr/low): hardware (254/0)
  Traffic Statistics for "inside":
        16433456 packets input, 2214576498 bytes
        24943662 packets output, 28202920165 bytes
        28768 packets dropped
      1 minute input rate 178 pkts/sec,  18825 bytes/sec
      1 minute output rate 267 pkts/sec,  306674 bytes/sec
      1 minute drop rate, 0 pkts/sec
      5 minute input rate 255 pkts/sec,  16417 bytes/sec
      5 minute output rate 422 pkts/sec,  548955 bytes/sec
      5 minute drop rate, 0 pkts/sec
```

Let's break this down line by line.

## General Interface Details
`Interface GigabitEthernet0/1 "inside", is up, line protocol is up`<br>
Interface number, name, status. The "is up" status can be up or administratively down. The like protocol status is either up (indicating there is a working cable plugged into the interface) or down (indicating the cable is either unplugged or incorrect).

`Hardware is i82546GB rev03, BW 1000 Mbps, DLY 10 usec`<br>
Hardware is the chip type used in the interface. The valid options here are:<br>

- i82542 - Intel PCI Fiber Gigabit card used on PIX platforms
- i82543 - Intel PCI-X Fiber Gigabit card used on PIX platforms
- i82546GB - Intel PCI-X Copper Gigabit used on ASA platforms
- i82547GI - Intel CSA Copper Gigabit used as backplane on ASA platforms
- i82557 - Intel PCI Copper Fast Ethernet used on ASA platforms
- i82559 - Intel PCI Copper Fast Ethernet used on PIX platforms
- VCS7380 - Vitesse Four Port Gigabit Switch used in SSM-4GE 

Displayed on this line is also the maximum bandwidth and delay that can be on this interface.

`Full-Duplex(Full-duplex), 100 Mbps(100 Mbps)`<br>
Duplex and speed settings. If the line is down, the configured values are displayed. If the line is up the negotiated or actual values will be in parenthesis.

`Input flow control is unsupported, output flow control is off`<br>
Optional message. Some examples are:<br>
If you do not configure a name, you see the following message: Available but not configured via nameif<br>
If an interface is a member of a redundant interface, you see the following message: Active member of Redundant5<br>
On a multi context firewall, in the system context you might see the following message: Available for allocation to a context<br>

`MAC address 442b.442b.442b, MTU 1500`<br>
This is the interfaces MAC address and configured MTU. If the interface name is not set the MTU will display "MTU not set".

`IP address 172.16.16.16, subnet mask 255.255.255.240`<br>
This is the interfaces IP address and subnet mask.

## Input Statistics
`16433456 packets input, 2581392514 bytes, 0 no buffer`<br>
The number of packets and bytes received on this interface. The "no buffer" indicates the number of failures from block allocations.

`Received 111 broadcasts, 0 runts, 0 giants`<br>
The number of broadcast packets received.<br>
**Runts** are the number of packets that are discarded because they are smaller than the minimum packet size, which is 64 bytes. Runts are usually caused by collisions. They might also be caused by poor wiring and electrical interference. <br>
**Giants** are the number of packets that are discarded because they exceed the maximum packet size. For example, any Ethernet packet that is greater than 1518 bytes is considered a giant. 

`0 input errors, 0 CRC, 0 frame, 0 overrun, 0 ignored, 0 abort`<br>
**Input errors** are the number of total input errors, including the types listed below. Other input-related errors can also cause the input error count to increase, and some datagrams might have more than one error; therefore, this sum might exceed the number of errors listed for the types below. <br>
**CRC errors** are the number of Cyclical Redundancy Check errors. When a station sends a frame, it appends a CRC to the end of the frame. This CRC is generated from an algorithm based on the data in the frame. If the frame is altered between the source and destination, the ASA notes that the CRC does not match. A high number of CRCs is usually the result of collisions or a station transmitting bad data.<br> 
**Frame errors** are bad frames that have packets with an incorrect length or bad frame checksums. This error is usually the result of collisions or a malfunctioning Ethernet device.<br>
**Overrun errors** are the number of times that the ASA was incapable of handing received data to a hardware buffer because the input rate exceeded the ASA capability to handle the data. <br>
**Ignored errors** are not used. The value is always 0.<br>
**Abort errors** are not used. The value is always 0.<br>

`0 pause input, 0 resume input`<br>
**Pause input** packets are unknown.<br>
**Resume input** packets are unknown.

`0 L2 decode drops`<br>
**L2 decode drop** packets are the number of packets dropped because the name is not configured (nameif command) or a frame with an invalid VLAN id is received. 

## Output Statistics

`24943232 packets output, 28662026144 bytes, 430 underruns`<br>
Number of packets and bytes output from this interface.<br>
**Undderrun errors** are the number of times that the transmitter ran faster than the ASA could handle. <br>

`0 pause output, 0 resume output`<br>
**Pause output** packets are unknown.<br>
**Resume output** packets are unknown.

`0 output errors, 0 collisions, 0 interface resets`<br>
**Output errors** are the number of frames not transmitted because the configured maximum number of collisions was exceeded. This counter should only increment during heavy network traffic. <br>
**Collisions** are the number of messages retransmitted due to an Ethernet collision (single and multiple collisions). This usually occurs on an overextended LAN (Ethernet or transceiver cable too long, more than two repeaters between stations, or too many cascaded multiport transceivers). A packet that collides is counted only once by the output packets. <br>
**Interface resets** are the number of times an interface has been reset. If an interface is unable to transmit for three seconds, the ASA resets the interface to restart transmission. During this interval, connection state is maintained. An interface reset can also happen when an interface is looped back or shut down<br>

`0 late collisions, 0 deferred`<br>
**Late collisions** is when the number of frames that were not transmitted because a collision occurred outside the normal collision window. A late collision is a collision that is detected late in the transmission of the packet. Normally, these should never happen. When two Ethernet hosts try to talk at once, they should collide early in the packet and both back off, or the second host should see that the first one is talking and wait. If you get a late collision, a device is jumping in and trying to send the packet on the Ethernet while the ASA is partly finished sending the packet. The ASA does not resend the packet, because it may have freed the buffers that held the first part of the packet. This is not a real problem because networking protocols are designed to cope with collisions by resending packets. However, late collisions indicate a problem exists in your network. Common problems are large repeated networks and Ethernet networks running beyond the specification. <br>
**Deferred** packets are the number of frames that were deferred before transmission due to activity on the link.<br>


`0 input reset drops, 0 output reset drops, 0 tx hangs`<br>
**Input reset drops** are the number of packets dropped in the RX ring when a reset occurs. <br>
**Output reset drops** are the number of packets dropped in the TX ring when a reset occurs. <br>
**TX hangs** is unknown.<br>

`input queue (blocks free curr/low): hardware (255/230)`<br>
The number of packets in the input queue. Values in the parenthesis are: blocks free currently / the lowest number of blocks free. 

`output queue (blocks free curr/low): hardware (254/0)`<br>
The number of packets in the output queue. Values in the parenthesis are: blocks free currently / the lowest number of blocks free. 


## Additional Interface Statistics

`Traffic Statistics for "inside":`<br>
`16433456 packets input, 2214576498 bytes`<br>
The number of packets and bytes received.

`24943662 packets output, 28202920165 bytes`<br>
The number of packets and bytes sent.

`28768 packets dropped`<br>
The number of packets dropped. Typically this counter increments for packets dropped on the accelerated security path (ASP), for example, if a packet is dropped due to an access list deny.
See the 'show asp drop' command for reasons for potential drops on an interface. 

`1 minute input rate 178 pkts/sec,  18825 bytes/sec`<br>
`1 minute output rate 267 pkts/sec,  306674 bytes/sec`<br>
`1 minute drop rate, 0 pkts/sec`<br>
`5 minute input rate 255 pkts/sec,  16417 bytes/sec`<br>
`5 minute output rate 422 pkts/sec,  548955 bytes/sec`<br>
`5 minute drop rate, 0 pkts/sec`<br>
Various interface bandwidth statistics.

<hr>
<br><br><br>
Source: [Cisco Documentation](http://www.cisco.com/en/US/docs/security/asa/asa84/command/reference/s3.html#wp1497263)



