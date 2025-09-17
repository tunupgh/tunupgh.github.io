---
layout: post
title: recv errors Cisco ASA VPN tunnel
date: 2015-01-22 18:18
comments: true
categories:
- cisco
- asa
- vpn
- troubleshooting
---
When I was troubleshooting a VPN tunnel on a Cisco ASA, 100% of the packets coming over the tunnel were being counted as `#recv errors`. It turns out that these errors can go up if there are anti-replay failures, corrupted packets, or other decapsulation errors. Because 100% of my packets were being counted for this I assumed it wasn't a corrupted packet or decapsulation error and began looking into anti-replay problems.

### Show the error count
Let's take a look at some show commands and configs.

<pre><code>ASA/pri/act(config)# show crypto ipsec sa peer 93.184.216.34
peer address: 93.184.216.34
    Crypto map tag: outside_map, seq num: 10, local addr: 11.11.11.11

      access-list ACL-TKY-VPN extended permit ip host 10.10.10.11 host 192.168.60.60
      local ident (addr/mask/prot/port): (10.10.10.11/255.255.255.255/0/0)
      remote ident (addr/mask/prot/port): (192.168.60.60/255.255.255.255/0/0)
      current_peer: 93.184.216.34

      #pkts encaps: 0, #pkts encrypt: 0, #pkts digest: 0
      <span style="color: #FF9999;">#pkts decaps: 105</span>, #pkts decrypt: 0, #pkts verify: 0
      #pkts compressed: 0, #pkts decompressed: 0
      #pkts not compressed: 0, #pkts comp failed: 0, #pkts decomp failed: 0
      #pre-frag successes: 0, #pre-frag failures: 0, #fragments created: 0
      #PMTUs sent: 0, #PMTUs rcvd: 0, #decapsulated frgs needing reassembly: 0
      #send errors: 0, <span style="color: #FF9999;">#recv errors: 105</span>

      local crypto endpt.: 11.11.11.11, remote crypto endpt.: 93.184.216.34

      path mtu 1500, ipsec overhead 74, media mtu 1500
      current outbound spi: D0E9F6C9
      current inbound spi : 76F99C4C
</code></pre>

The ACL for this tunnel is only one line:

```
access-list ACL-TKY-VPN extended permit ip host 10.10.10.11 host 192.168.60.60
```

### Enable Debugs

Let's turn on the following debug and take a look:<br>
`debug crypto ipsec 1`

`Jan 19 2015 20:00:43: %ASA-4-402116: IPSEC: Received an ESP packet (SPI= 0x76F99C4C, sequence number= 0x2D) from 93.184.216.34 (user= 93.184.216.34) to 11.11.11.11.  The decapsulated inner packet doesn't match the negotiated policy in the SA.  The packet specifies its destination as 10.10.10.11, its source as 10.14.155.11, and its protocol as icmp.  The SA specifies its local proxy as 10.10.10.11/255.255.255.255/ip/0 and its remote_proxy as 192.168.60.60/255.255.255.255/ip/0.`

This is a fantastic clue! What the debug is telling us is that when traffic comes over the tunnel it's expected to look like:

From: `192.168.60.60` To: `10.10.10.11`

But instead it's looking like this:

From: `10.14.155.11` To: `10.10.10.11`

This likely means that the remote side either does not have the same ACL defined on it OR it is NATing incorrectly.



