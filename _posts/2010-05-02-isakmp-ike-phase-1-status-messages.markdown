---
layout: post
title: ISAKMP (IKE Phase 1) status messages MM_WAIT_MSG#
categories:
- cisco
- troubleshooting
- featured
- isakmp
- mm_wait_msg1
- mm_wait_msg2
- mm_wait_msg3
- mm_wait_msg4
- mm_wait_msg5
- mm_wait_msg6
- phase 1
- state
- status
comments: true
---
## ISAKMP (IKE Phase 1) Negotiations States
The MM\_WAIT\_MSG state can be an excellent clue into why a tunnel is not forming. If your firewall is hanging at a specific state review this graph below to find where along the path the VPN is failing.

## ASA ISAKMP STATES

![IKE Phase Messages - IMG](/images/IKE_Phase1_MSGs.png)

<p style="text-align: center;">Graph source: tunnelsup.com</p>

These are the possible ISAKMP negotiation states on an ASA firewall. ISAKMP stands for: The Internet Security Association and Key Management Protocol

<ul>
	<li>MM_WAIT_MSG2 Initiator<span style="color: #008000;">
Initial DH public key sent to responder. Awaiting initial contact reply from other side.</span>
Initiator sends encr/hash/dh ike policy details to create initial contact. Initiator will wait at MM_WAIT_MSG2 until it hears back from its peer. If stuck here it usually means the other end is not responding. This could be due to no route to the far end or the far end does not have ISAKMP enabled on the outside or the far end is down.</li>
</ul>
<ul>
	<li>MM_WAIT_MSG3 Receiver<span style="color: #008000;">
Receiver is sending back its IKE policy to the initiator.</span><span style="color: #008000;"><span style="color: #000000;">
Initiator sends encr/hash/dh ike policy details to create initial contact. Initiator will wait at MM_WAIT_MSG2 until it hears back from its peer. Hang ups here may also be due to mismatch device vendors, a router with a firewall in the way, or even ASA version mismatches.</span></span></li>
</ul>
<ul>
	<li>MM_WAIT_MSG4<span style="color: #008000;"><span style="color: #000000;"> Initiator
<span style="color: #008000;">Initiator is sending the Pre-Shared-Key hash to its peer.</span>
Initiator sends a hash of its PSK. Initiator will stay at MSG4 until it gets a PSK back from its peer. If the receiver is missing a tunnel group or PSK the initiator will stay at MM_WAIT_MSG4
</span></span></li>
</ul>
<ul>
	<li>MM_WAIT_MSG5 Receiver
<span style="color: #008000;">Receiver is sending its PSK hash to its peer.</span><span style="color: #008000;"><span style="color: #000000;">
Receiver does not yet check if PSK hashes match. If receiver has a tunnel-group and PSK configured for this peer it will send the PSK hash to the peer. If PSKs dont match, receiver will stay at MM_WAIT_MSG5. I have also seen the tunnel stop here when NAT-T was on when it needed to be turned off.
</span></span></li>
</ul>
<ul>
	<li>MM_WAIT_MSG6 Initiator
<span style="color: #008000;">Initiator checks if PSK hashes match.</span>
If PSK keys match, Initiator becomes MM_ACTIVE and lets receiver know of match. If PSK doesnt match, initiator stays at MM_WAIT_MSG6. I have also seen the tunnel stop here when NAT-T was on when it needed to be turned off.
However, if the state goes to MSG6 then the ISAKMP gets reset that means phase 1 finished but phase 2 failed. Check that IPSEC settings match in phase 2 to get the tunnel to stay at MM_ACTIVE.</li>
</ul>
<ul>
	<li>AM_ACTIVE / MM_ACTIVE<span style="color: #008000;">
The ISAKMP negotiations are complete. Phase 1 has successfully completed.</span></li>
</ul>

## PIX ISAKMP STATES
<ul>
<ul>
	<li>MM_NO_STATE</li>
</ul>
</ul>
<span style="color: #008000;">ISAKMP SA has been created but nothing else has happened yet.</span>
<ul>
<ul>
	<li>MM_SA_SETUP</li>
</ul>
</ul>
<span style="color: #008000;">The peers have agreed on parameters for the ISAKMP SA.</span>
<ul>
<ul>
	<li>MM_KEY_EXCH</li>
</ul>
</ul>
<span style="color: #008000;">The peers have exchanged Diffie-Hellman public keys and have generated a shared secret. The I SAKMP SA remains unauthenticated.</span>
<ul>
<ul>
	<li>MM_KEY_AUTH</li>
</ul>
</ul>
<span style="color: #008000;">The ISAKMP SA has been authenticated. If the router initiated this exchange, this state trans itions immediately to QM_IDLE and a Quick mode exchange begins.</span>
<ul>
<ul>
	<li>AG_NO_STATE</li>
</ul>
</ul>
<span style="color: #008000;">The ISAKMP SA has been created but nothing else has happened yet.</span>
<ul>
<ul>
	<li>AG_INIT_EXCH</li>
</ul>
</ul>
<span style="color: #008000;">The peers have done the first exchange in Aggressive mode but the SA is not authenticated.</span>
<ul>
<ul>
	<li>AG_AUTH</li>
</ul>
</ul>
<span style="color: #008000;">The ISAKMP SA has been authenticated. If the router initiated this exchange, this state transitions immediately to QM_IDLE and a Quick mode exchange begins.</span>
<ul>
<ul>
	<li>QM_IDLE</li>
</ul>
</ul>
<span style="color: #008000;">The ISAKMP negotiations are complete. Phase 1 successfully completed. It remains authenticated with its peer and may be used for subsequent Quick mode exchanges.</span>

## What is the difference between MM and AM?
Main mode vs Aggressive mode. Here is a image taken from <a href="https://supportforums.cisco.com/docs/DOC-8125">Cisco's website</a> to show the difference.

![MM AM - IMG](/images/ws22.gif)

As you can see the Main mode is the same as the flowchart at the top of the page. Aggressive mode only uses 4 steps to establish the tunnel.


## Troubleshooting ISAKMP Or Phase 1 VPN connections

When troubleshooting VPNs, a very common problem is phase 1 not establishing correctly. Here's a quick checksheet to make sure you have the configuration correct.
<ul>
	<li>Verify ISAKMP parameters match exactly.</li>
	<li>Verify pre-shared-keys match exactly.</li>
	<li>Check that each side has a route to the peer address that you are trying to form a tunnel with.</li>
	<li>Verify ISAKMP is enabled on the outside interfaces.</li>
	<li>Is ESP traffic permitted in through the outside interface?</li>
	<li>Is UDP port 500 open on the outside ACL?</li>
	<li>Some situations require that UDP port 4500 is open for the outside.</li>
</ul>
