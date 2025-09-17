---
layout: post
title: Tips on troubleshooting VPN's in general
categories:
- cisco
- vpn
- troubleshooting
comments: true
---
<h2>Debugs And Show Commands</h2>
To gain more info on where the problem might be, try these show commands and debugs.
<ul>
	<li>show crypto isakmp sa</li>
<span style="color: #008000;">Showing status of ISAKMP negotiations</span>
	<li>show crypto ipsec sa [peer 1.1.1.1]</li>
<span style="color: #008000;">Show status of IPSEC tunnels</span>
	<li>show log</li>
	<li>debug crypto isakmp</li>
	<li>debug crypto ipsec</li>
	<li>debug crypto condition peer 1.1.1.1</li>
</ul>
<h2>Knowing where to look for problems</h2>
VPN problems are usually very easy to fix once you know where the problem is. Understand that VPN tunnels are a multi-step process. Knowing this you should be able to step through the tunnel being built to find where it got hung up at and stopped working.
<ul>
	<li>Tunnel isn't even trying to start</li>
<span style="color: #008000;">ISAKMP/IPSEC isn't enabled or applied to an interface. VPN ACL isn't catching any interesting traffic to fire up the tunnel. Routes/NAT's are misdirecting traffic out the wrong interface.</span>
	<li><span style="color: #008000;">ISAKMP Phase 1 doesn't complete all the way.</span></li>
<span style="color: #008000;">There are lots of reasons why ISAKMP Phase 1 won't fully establish. Look over some of the common reasons </span><a href="/cisco-vpn-troubleshooting-encaps-but-no-decaps"><span style="color: #008000;">here.</span></a>
	<li>Phase 1 establishes but Phase 2 doesn't fully establish</li>
<span style="color: #008000;">It is possible that the Phase 2 IPSEC attributes don't match but this usually indicates a malformed Phase 1.</span>
	<li>Phase 1 and 2 establish but traffic still isn't getting there</li>
<span style="color: #008000;">Remember, the VPN tunnel is just in charge of getting the secure line open. Networking rules still apply. Check routes/ACL's/NAT's etc.</span></ul>
