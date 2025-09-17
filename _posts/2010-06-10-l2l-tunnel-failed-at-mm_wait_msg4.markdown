---
layout: post
title: L2L Tunnel Failed at MM_WAIT_MSG4
categories:
- troubleshooting
- asa
- cisco
- debug
- mm_wait_msg4
- phase 1
comments: true
---
First be sure to read [this post on MM WAIT MSG numbers](http://www.tunnelsup.com/isakmp-ike-phase-1-status-messages/). It goes over all of the ISAKMP states.

MM\_WAIT\_MSG4 is the stage where the firewall that initiated the tunnel is sending its pre-shared key hash to the receiver. This is NOT the stage that actually checks to see if the pre-shared keys match, it only exchanges the hashes for them.

The Initiator will stay at MSG4 until it gets a PSK back from its peer. If the receiver is missing a tunnel group or PSK the initiator will stay at MM\_WAIT\_MSG4

If the tunnel has to pass through a firewall to reach both end points you might have problems with the way traffic is passed through that middle firewall.

The tunnel can sometimes get stuck here due to incompatible vendor types. Try to get both devices on a modern version of software to try to eliminate any vendor mismatch issues.

This can sometimes fail due to mismatched ASA versions (rare).

Recently I had this problem pop up and I wanted to deconstruct the debug message leading up to the problem
<pre><code><span style="color: #008000;">! --- Tunnel initiated </span><span style="color: #008000;">- OK lets go!</span>
Jun 10 2010 17:36:05: %ASA-7-715077: Pitcher: received a key acquire message, spi 0x0
Jun 10 2010 17:36:05: %ASA-5-713041: IP = 233.22.22.233, IKE<span style="color: #0000ff;"> Initiator</span>: New Phase 1, Intf Inside_Network, IKE Peer 233.22.22.233  local Proxy Address 192.168.245.120, remote Proxy Address 10.3.10.122,  Crypto map (MAP-VPN)
Jun 10 2010 17:36:05: %ASA-7-715046: IP = 233.22.22.233, constructing ISAKMP SA payload
Jun 10 2010 17:36:05: %ASA-7-715046: IP = 233.22.22.233, constructing NAT-Traversal VID ver 02 payload
Jun 10 2010 17:36:05: %ASA-7-715046: IP = 233.22.22.233, constructing NAT-Traversal VID ver 03 payload
Jun 10 2010 17:36:05: %ASA-7-715046: IP = 233.22.22.233, constructing Fragmentation VID + extended capabilities payload
Jun 10 2010 17:36:05: %ASA-7-713236: IP = 233.22.22.233, IKE_DECODE <span style="color: #0000ff;">SENDING Message</span> (msgid=0) with payloads : HDR + SA (1) + VENDOR (13) + VENDOR (13) + VENDOR (13) + NONE (0) total length : 148
Jun 10 2010 17:36:05: %ASA-7-713236: IP = 233.22.22.233, IKE_DECODE <span style="color: #0000ff;">RECEIVED Message</span> (msgid=0) with payloads : HDR + SA (1) + VENDOR (13) + NONE (0) total length : 108
Jun 10 2010 17:36:05: %ASA-7-715047: IP = 233.22.22.233,<span style="color: #0000ff;"> processing SA payload</span>
Jun 10 2010 17:36:05: %ASA-7-713906: IP = 233.22.22.233, <span style="color: #0000ff;">Oakley proposal is acceptable</span>
<span style="color: #008000;">! --- At this point the firewall's have agreed on the same ISAKMP proposals</span>
Jun 10 2010 17:36:05: %ASA-7-715047: IP = 233.22.22.233, processing VID payload
Jun 10 2010 17:36:05: %ASA-7-715049: IP = 233.22.22.233, Received Fragmentation VID
Jun 10 2010 17:36:05: %ASA-7-715064: IP = 233.22.22.233, IKE Peer included IKE fragmentation capability flags:  Main Mode:        True  Aggressive Mode:True
Jun 10 2010 17:36:05: %ASA-7-715046: IP = 233.22.22.233, constructing ke payload
Jun 10 2010 17:36:05: %ASA-7-715046: IP = 233.22.22.233, constructing nonce payload
Jun 10 2010 17:36:05: %ASA-7-715046: IP = 233.22.22.233, constructing Cisco Unity VID payload
Jun 10 2010 17:36:05: %ASA-7-715046: IP = 233.22.22.233, constructing xauth V6 VID payload
Jun 10 2010 17:36:05: %ASA-7-715048: IP = 233.22.22.233, Send IOS VID
Jun 10 2010 17:36:05: %ASA-7-715038: IP = 233.22.22.233, Constructing ASA spoofing IOS Vendor ID payload (version: 1.0.0, capabilities: 20000001)
Jun 10 2010 17:36:05: %ASA-7-715046: IP = 233.22.22.233, constructing VID payload
Jun 10 2010 17:36:05: %ASA-7-715048: IP = 233.22.22.233, Send Altiga/Cisco VPN3000/Cisco ASA GW VID
Jun 10 2010 17:36:05: %ASA-7-713236: IP = 233.22.22.233, IKE_DECODE <span style="color: #0000ff;">SENDING Message</span> (msgid=0) with payloads : HDR + KE (4) + NONCE (10) + VENDOR (13) + VENDOR (13) + VENDOR (13) + VENDOR (13) + NONE (0) total length : 256
<span style="color: #008000;">! --- All the syslogs above happened in the same second. All of a sudden things slowed down. 5 seconds passed which made me concerned already.</span>
Jun 10 2010 17:36:10: %ASA-7-715077: Pitcher: received a key acquire message, spi 0x0
<span style="color: #008000;">! --- Wait, what? A new key acquire message, this isn't right...</span>
Jun 10 2010 17:36:10: %ASA-6-713219: IP = 233.22.22.233, Queuing KEY-ACQUIRE messages to be processed when P1 SA is complete.
Jun 10 2010 17:36:13: %ASA-7-713236: IP = 233.22.22.233, IKE_DECODE <span style="color: #0000ff;">RESENDING</span> Message (msgid=0) with payloads : HDR + KE (4) + NONCE (10) + VENDOR (13) + VENDOR (13) + VENDOR (13) + VENDOR (13) + NONE (0) total length : 256
<span style="color: #008000;">! --- Resending the payload? That's not a good sign...</span>
Jun 10 2010 17:36:13: %ASA-7-713236: IP = 233.22.22.233, IKE_DECODE <span style="color: #0000ff;">RECEIVED</span> Message (msgid=0) with payloads : HDR + NOTIFY (11) + NONE (0) total length :68
Jun 10 2010 17:36:13: %ASA-7-713236: IP = 233.22.22.233, IKE_DECODE <span style="color: #0000ff;">RECEIVED</span> Message (msgid=0) with payloads : HDR + NOTIFY (11) + NONE (0) total length :68
Jun 10 2010 17:36:13: %ASA-5-713904: IP = 233.22.22.233, <span style="color: #800000;">Received an un-encrypted INVALID_COOKIE notify message, dropping</span>
<span style="color: #008000;">! --- un-encrypted cookie? Why did the other side send us an un-encrypted packet? Could it be because the other side doesn't have any information for this peer??</span>
Jun 10 2010 17:36:13: %ASA-4-713903: IP = 233.22.22.233, Information Exchange processing failed
Jun 10 17:36:13 [IKEv1]: IP = 233.22.22.233, Information Exchange processing failed
</code></pre>

The ultimate reason why this tunnel didn't form is because the other side had the wrong Peer IP defined for the tunnel-group/pre-shared key. So when this side sent its isakmp proposals the other side only checked if it had a crypto map statement for this peer, it didn't check if it had a tunnel-group defined. When they tried to exchange the pre-shared keys the remote end realized it doesn't have any pre-shared key for this peer and just stopped responding. When this side tried to send another packet the remote end sent some cookie over which of course was not encrypted because it doesn't have the right peer IP to do the encryption with. The debugs above are from an ASA 8.0.4 code connecting to a Cisco VPN concentrator running unknown code.
