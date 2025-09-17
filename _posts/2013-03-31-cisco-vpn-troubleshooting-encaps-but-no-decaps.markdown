---
layout: post
title: Cisco VPN troubleshooting - encaps but no decaps
categories:
- cisco
- asa
- decaps
- encaps
- ipsec
- troubleshooting
- vpn
comments: true
---
Suppose you are trying to troubleshoot a site to site VPN tunnel that is designed like this:
![ASA Site to site diagram - IMG](/images/asasitetosite.jpg)

Upon doing `show ipsec sa peer` on the blue ASA you see the following:

```
interface: OUTSIDE
Crypto map tag: MAP-OUTSIDE, seq num: 200, local addr: 11.11.11.11
local ident (addr/mask/prot/port): (192.168.11.0/255.255.255.0/0/0)
remote ident (addr/mask/prot/port): (172.16.22.0/255.255.255.0/0/0)
current_peer: 22.22.22.22
#pkts encaps: 61, #pkts encrypt: 61, #pkts digest: 61
#pkts decaps: 0, #pkts decrypt: 0, #pkts verify: 0
#pkts compressed: 0, #pkts decompressed: 0
#pkts not compressed: 61, #pkts comp failed: 0, #pkts decomp failed: 0
#pre-frag successes: 0, #pre-frag failures: 0, #fragments created: 0
#PMTUs sent: 0, #PMTUs rcvd: 0, #decapsulated frgs needing reassembly: 0
#send errors: 0, #recv errors: 0
```
The problem above shows that Phase 1 of the tunnel is successfully establishing but phase 2 has problems. Specifically the firewall is encrypting packets but not decrypting them.

If an ASA or router is getting encaps but not decaps, this means it is encrypting the data and sending it but has not received anything to decrypt in return.
<ul>
	<li>Verify the other end has a route outside for the interesting traffic.</li>
	<li>Check that both VPN ACL's are not mismatched.</li>
	<li>Double check NAT's to make sure the traffic is not NAT'ing correctly.</li>
	<li>Is what you are trying to ping even responding back? Often what you're sending traffic to is not able to accept or is not responding to this traffic. I prefer to put a packet capture on the remote end firewall to see if the traffic is coming back into that firewall.</li>
</ul>
