---
layout: post
title: What is ping?
date: 2014-12-30 22:15
comments: true
categories:
- misc
- ping
---
A "ping" is a way for one computer to check if it can reach another computer using the networking IP protocol. The source computer can do a ping command to check if a destination computer is reachable, replies to pings, and the time it took to get there and back. The result will be the round-trip time it took for an IP packet to make it to the destination and back. The name comes from sonar terminology which would send a pulse and wait for the echo response.

![](/images/ping-icmp.gif)

Personal computers come equipped with the ping tool installed by default. Simply open a cmd or terminal window and type `ping example.com`. The result will look something like this:

```
ping example.com
PING example.com (93.184.216.34): 56 data bytes
64 bytes from 93.184.216.34: icmp_seq=0 ttl=57 time=16.764 ms
64 bytes from 93.184.216.34: icmp_seq=1 ttl=57 time=16.836 ms
64 bytes from 93.184.216.34: icmp_seq=2 ttl=57 time=13.934 ms
64 bytes from 93.184.216.34: icmp_seq=3 ttl=57 time=14.446 ms
64 bytes from 93.184.216.34: icmp_seq=4 ttl=57 time=14.236 ms

--- example.com ping statistics ---
5 packets transmitted, 5 packets received, 0.0% packet loss
round-trip min/avg/max/stddev = 13.934/15.243/16.836/1.282 ms
```

The output above shows that 5 pings were sent to example.com and round trip time it took for the ping packet to make it there and back was 15ms or 0.015 seconds. 

The `ttl` in the response above indicates "time to live". This is the hop limit that limits the life of the packet. By default the TTL of a ping is 255. This decrements by 1 for every router or hop the packets goes through in order to get to its destination. This mechanism is put in place to avoid packet storms and routing loops. If a packet didn't have a TTL and couldn't reach its destination it could keep bouncing around the network forever.

The technology used to make ping work is Internet Control Message Protocol (ICMP). A ping works by sending an ICMP echo request packet to the destination. When the destination receives it, it will respond with an ICMP echo reply. Often the word ping and ICMP are used interchangeably.


### Using Ping to Troubleshoot Network Connectivity Issues
The ping command is a simple yet powerful tool to test basic connectivity between two computers. Not all computers will reply to a echo request packet. By default, Windows computers block incoming pings using the Windows Firewall. 

If a system administrator or user is having a problem getting a computer on the network they may try the following tests:

1. Ping the IP of the computer itself.
2. Ping the IP of the default gateway.
3. Ping the IP of the destination system.

By running these three tests, we can determine where the network problem exists. If test 1 fails we know there is a problem with the interface of the computer itself and it should be checked. If test 2 fails we know there's an issue between this computer and the very next hop the computer is connected to. If test 3 fails it may mean a number of things are wrong such as a firewall blocking outbound connections over ICMP, the destination device is blocking ICMP, there isn't a route to that system, or that system may be down.

### ICMP types and codes
Ping or ICMP packets have a specific 'type' flag set on them to determine what type of ping this is. For instance the type could be echo request or echo reply. Here is a table with [common ICMP types](http://www.iana.org/assignments/icmp-parameters/icmp-parameters.xhtml#icmp-parameters-codes-8).

<table class="table table-bordered table-striped table-hover">
      <thead>
        <tr>
          <th>Type</th>
          <th>Name</th>
          <th>Code</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>0</td>
          <td>Echo Reply</td>
          <td>0 - no code</td>
        </tr>
        <tr>
          <td>3</td>
          <td>Destination Unreachable</td>
          <td>0 - net unreachable<br>1 - host unreachable<br>3 - port unreachable<br>5 - source route failed<br>6 - dest network unknown<br>7 - dest host unkonwn</td>
        </tr>
        <tr>
          <td>5</td>
          <td>Redirect</td>
          <td></td>
        </tr>
        <tr>
          <td>8</td>
          <td>Echo Request</td>
          <td>0 - no code</td>
        </tr>
        <tr>
          <td>9</td>
          <td>Router Advertisement</td>
          <td></td>
        </tr>
        <tr>
          <td>10</td>
          <td>Router Solicitation</td>
          <td></td>
        </tr>
        <tr>
          <td>11</td>
          <td>Time Exceeded</td>
          <td>0 - ttl exceeded in transit<br>1 - fragment reassembly time exceeded</td>
        </tr>
        <tr>
          <td>13</td>
          <td>Timestamp</td>
          <td></td>
        </tr>
        <tr>
          <td>14</td>
          <td>Timestamp Reply</td>
          <td></td>
        </tr>
      </tbody>
</table>

### Unique Characteristics of ICMP
An ICMP packet is identified as having [protocol 1](http://www.iana.org/assignments/protocol-numbers/protocol-numbers.xhtml). This makes it different than TCP or UDP which use different protocols. 

When a packet is sent using TCP or UDP it uses a source port and destination port. When the destination system replies it switches the source and destination ports to let all of the devices in the network path know this is a return packet. ICMP works different by completely tearing down the original echo request and creating a fresh new echo reply packet. Because of this ICMP is not a stateful protocol and often needs to be handled separately by networking devices to be permitted through.
