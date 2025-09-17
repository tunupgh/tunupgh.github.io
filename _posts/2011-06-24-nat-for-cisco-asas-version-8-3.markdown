---
layout: post
title: NAT for Cisco ASA's version 8.3+
categories:
- cisco
- featured
- scripts
- '8.3'
- asa
- nat
comments: true
---
There are two major kinds of NAT in 8.3+ Auto NAT and Manual NAT. Auto is done inside the object and cannot take into consideration the destination of the traffic. Manual is done in global configuration and can NAT either the source IPs and destination IPs.

### Auto NAT

The new term autoNAT is used in 8.3. Auto NAT is when the  NAT command appears INSIDE the object statement on the firewall.  There are two major variants of auto NAT: dynamic and static. Auto NAT is also sometimes referenced as Network Object NAT because the configuration is done within the network object.

##### Regular Dynamic PAT
To create a many-to-one NAT where the entire inside network is getting PATd to a single outside IP do the following.

Old 8.2 command:
<pre><code><span style="color: #993300;">nat (inside) 1 10.0.0.0 255.255.255.0</span>
<span style="color: #993300;">global (outside) 1 interface</span>
</code></pre>

New 8.3 equivalent command:
<pre><code><span style="color: #00F000;">object network inside-net</span>
  <span style="color: #00F000;">subnet 10.0.0.0 255.255.255.0</span>
<span style="color: #00F000;">nat (inside,outside) <strong>dynamic</strong> interface</span>
</pre></code>


Note: the interface command is the 2nd interface in the nat statement, in this case the outside.

##### Static Auto-NAT

To create a one to one NAT within the object like when you have a webserver in your DMZ you can do the following NAT configuration.
<pre><code><span style="color: #00F000;">object network dmz-webserver</span>
  <span style="color: #00F000;">host 192.168.1.23</span>
<span style="color: #00F000;">nat (dmz,outside) <strong>static</strong> 209.165.201.28</span>
</pre></code>

Please note, the `nat (inside,outside)` part of these commands are a lot easier to read in 8.3. The first interface is the interface the traffic is coming into the ASA on and the second interface is the interface that this traffic is going out of the ASA on. So the command nat (dmz,outside) static 209.165.201.28 should be read as NAT the IP address 192.168.1.23 to 209.165.201.28 if the traffic is coming in on the dmz interface and going out the outside interface, or vice versa. This will not NAT traffic coming from the inside going to the DMZ, nor should it NAT the traffic coming from the DMZ going to the inside.

##### Using the `any` interface in the NAT statement

ASA 8.3 introduces the `any` interface when configuring NAT. For instance if you have a system on the DMZ that you wish to NAT not only to the outside interface, but to any interface you can use this command:
<pre><code><span style="color: #00F000;">object network dmz-webserver</span>
  <span style="color: #00F000;">host 192.168.1.23</span>
<span style="color: #00F000;">nat (dmz,any) static 200.200.200.200</span>
</pre></code>

This makes it so users on the inside can web to 200.200.200.200 and if traffic is routed to the firewall it will NAT it to the real IP in the DMZ.

##### Port forwarding using Auto NAT

Suppose you have 2 web servers in your DMZ but you only have 1 IP address. You can configure port forwarding using the auto NAT feature in the following way:
<pre><code><span style="color: #00F000;">object network dmz-webserver1</span>
  <span style="color: #00F000;">host 192.168.1.25</span>
<span style="color: #00F000;">nat (dmz,outside) static interface service tcp 8000 www</span>
<span style="color: #00F000;">object network dmz-webserver2</span>
  <span style="color: #00F000;">host 192.168.1.23</span>
<span style="color: #00F000;">nat (dmz,outside) static interface service tcp 8080 www</span>
</pre></code>

This will make it so if you go to the IP address of the outside interface over port 8000 it will take you to 192.168.1.25 port 80 but if you go there using port 8080 it will take you to 192.168.1.23 port 80.

Confused yet? I hope not because its about to get weird

### Manual NAT or Twice NAT or Policy NAT or Reverse NAT

The limitation that Auto NAT has is that it cannot take the destination into consideration when conducting its NAT. This also of course results in it not being able to alter the destination address either. To accomplish either of these tasks you must use manual NAT.

All of these terms are **identical**: Manual NAT, Twice NAT, Policy NAT, Reverse NAT. Dont be confused by fancy mumbo jumbo.

##### Policy NAT Exemption aka NAT Zero aka No NAT

In ASA 8.3 code this is known as Policy NAT exemption. This is commonly used to not NAT traffic over a VPN tunnel.
<pre><code><span style="color: #00F000;">object network inside-net</span>
  <span style="color: #00F000;">subnet 10.0.0.0 255.255.255.0</span>
<span style="color: #00F000;">object network vpn-subnets</span>
  <span style="color: #00F000;">range 10.1.0.0 10.5.255.255</span>
<span style="color: #00F000;">nat (inside,outside) source static inside-net inside-net destination static vpn-subnets vpn-subnets</span>
</pre></code>

##### Policy NAT exemption for incoming remote access VPNs

In order for a packet to come in through a firewall from a lesser security interface to a higher security interface it must have a translation and an ACL to permit it through. If you are setting up remote access VPN then the ACL is usually bypassed since its tunneled traffic. There still needs to be a translation. This is completed by doing the following (Note the order of the interfaces in the NAT statement):

<pre><code><span style="color: #00F000;">object-group network OBJ-INSIDE-NETWORKS</span>
  <span style="color: #00F000;">network-object 172.16.200.0 255.255.255.0</span>
<span style="color: #00F000;">object network obj-172.16.101.0</span>
  <span style="color: #00F000;">subnet 172.16.101.0 255.255.255.0</span>
<span style="color: #00F000;">nat (OUTSIDE,INSIDE) source static obj-172.16.101.0 obj-172.16.101.0 destination static OBJ-INSIDE-NETWORKS OBJ-INSIDE-NETWORKS</span>
</pre></code>

##### Dynamic Policy NAT

This is when you want to specify an ACL for your NAT traffic to match on and if it matches that ACL then NAT it to something

Suppose you are trying to build a VPN tunnel to another site. The problem is that your private IP addresses are overlapping with their private IP addresses so they tell you that you MUST come from 172.27.27.27. If this was a static one to one translation it wouldnt be so hard but in this case we have many users all needing to use that IP address.

In the pre 8.3 configuration your code would look something like this:
<pre><code><span style="color: #993300;">access-list ACL-VENDOR-VPN-NAT extended permit ip 192.168.1.0 255.255.255.0 host 172.16.75.5</span>
<span style="color: #993300;">nat (inside) 3 access-list ACL-VENDOR-VPN-NAT</span>
<span style="color: #993300;">global (outside) 3 172.27.27.27</span>
</pre></code>

In the new ASA 8.3 config the code looks like this:
<pre><code><span style="color: #00F000;">object network inside-net</span>
  <span style="color: #00F000;">subnet 192.168.1.0 255.255.255.0</span>
<span style="color: #00F000;">object network vendor-vpn-nat</span>
  <span style="color: #00F000;">host 172.16.75.5</span>
<span style="color: #00F000;">object network translated-ip</span>
  <span style="color: #00F000;">host 172.27.27.27</span>
<span style="color: #00F000;">nat (inside,outside) source dynamic inside-net translated-ip destination static vendor-vpn-nat vendor-vpn-nat</span>
</pre></code>


### Miscellaneous Notes

##### Use real IPs in access-lists

In ASA version 8.3 you must specify the real IP and not the translate IP. For instance to permit your traffic  to the webserver through the outside ACL you must put:

access-list ACL-OUTSIDE-IN extended permit tcp any host 192.168.1.25 eq 80

This is a major change from pre 8.3 which would specify the public or NATd IP address.

### Show commands

To view this configuration you must check two places to see what is being NATd.

`show run object`

`show run nat`

The command show run object in-line is sometimes useful to when using the pipe commands.

You can also see the order of NAT and number of NAT translation hit counts with:

`show nat`

### Optional Destination keyword in manual NAT

The destination keyword and addresses in the manual NAT command is optional.  This means that both of these configurations do the same work:
<pre><code><span style="color: #00F000;">object network inside-net</span>
<span style="color: #00F000;">subnet 10.0.0.0 255.255.255.0</span>
<span style="color: #00F000;">nat (inside,outside) dynamic interface</span>
<span style="color: #00F000;">!</span>
<span style="color: #00F000;">object network inside-net</span>
<span style="color: #00F000;">subnet 10.0.0.0 255.255.255.0</span>
<span style="color: #00F000;">nat (inside,outside) source dynamic inside-net interface</span>
</pre></code>


### NAT order and after-auto NATing

The order of operation in NAT commands is documented here:

<a href="http://www.cisco.com/en/US/partner/docs/security/asa/asa83/configuration/guide/nat_overview.html#wp1118157">http://www.cisco.com/en/US/partner/docs/security/asa/asa83/configuration/guide/nat_overview.html#wp1118157</a>

The NAT operation will only take place once. Once there is a match on a NAT it will stop looking down the line to see whether it needs to NAT this traffic or not. The order of operation for this is like so:
<ol>
	<li>Twice NAT statements</li>
	<li>Auto NAT statements</li>
	<li>After-Auto NAT statements</li>
</ol>
Let's say you have a Manual or Twice NAT that you want to be considered AFTER all of the auto NATs. You can specify this by adding the after-auto keyword which would look something like this:

<span style="color: #00F000;">`nat (inside,outside) after-auto source dynamic any`</span>

### Using Descriptions

The description keyword can be added to the end of a manual NAT statement to keep things more organized like so:
<pre><code><span style="color: #00F000;">nat (OUTSIDE,INSIDE) source static obj-172.16.101.0 obj-172.16.101.0 destination static OBJ-INSIDE-NETWORKS OBJ-INSIDE-NETWORKS description ANYCON-NONAT</span>
</pre></code>


### Inactive NAT statements

You may deactivate a manual NAT statement by adding the inactive keyword at the end of the statement like so:
<pre><code><span style="color: #00F000;">nat (OUTSIDE,INSIDE) source static obj-172.16.101.0 obj-172.16.101.0 destination static OBJ-INSIDE-NETWORKS OBJ-INSIDE-NETWORKS inactive</span>
</pre></code>

### Cisco Documentation on NAT for 8.3

CLI NAT configuration guide for ASA 8.3
<a href="http://www.cisco.com/en/US/partner/docs/security/asa/asa83/configuration/guide/nat_overview.html">http://www.cisco.com/en/US/partner/docs/security/asa/asa83/configuration/guide/nat_overview.html</a>

Upgrading to ASA 8.3  What you need to know
<a href="https://supportforums.cisco.com/docs/DOC-12690">https://supportforums.cisco.com/docs/DOC-12690</a>

Video examples and tutorial
<a href="https://supportforums.cisco.com/docs/DOC-12324">https://supportforums.cisco.com/docs/DOC-12324</a>

<span id="307d6c73_b603_4925_99da_e8f83599f5eb"><object id="embedded_playerb198756c208b6" width="520" height="328" classid="clsid:d27cdb6e-ae6d-11cf-96b8-444553540000" codebase="http://download.macromedia.com/pub/shockwave/cabs/flash/swflash.cab#version=6,0,40,0"><param name="codebase" value="http://download.macromedia.com/pub/shockwave/cabs/flash/swflash.cab#version=5,0,30,0" /><param name="pluginspage" value="http://www.macromedia.com/go/getflashplayer" /><param name="allowfullscreen" value="true" /><param name="allowscriptaccess" value="always" /><param name="wmode" value="transparent" /><param name="quality" value="high" /><param name="base" value="http://videosvc.jivesoftware.com" /><param name="flashvars" value="v=0&amp;l=[{video_id:'b198756c208b6',logo_file:'https://supportforums.cisco.com/servlet/JiveServlet/downloadImage/6472/cisco_logo.png'}]" /><param name="src" value="http://videosvc.jivesoftware.com/plugins/player.swf?p=document&amp;auth_token=f9452220e1f763ff8e22c12e7b2c58919415c30b8951481284b76921d6012549" /><embed id="embedded_playerb198756c208b6" width="520" height="328" type="application/x-shockwave-flash" src="http://videosvc.jivesoftware.com/plugins/player.swf?p=document&amp;auth_token=f9452220e1f763ff8e22c12e7b2c58919415c30b8951481284b76921d6012549" codebase="http://download.macromedia.com/pub/shockwave/cabs/flash/swflash.cab#version=5,0,30,0" pluginspage="http://www.macromedia.com/go/getflashplayer" allowfullscreen="true" allowscriptaccess="always" wmode="transparent" quality="high" base="http://videosvc.jivesoftware.com" flashvars="v=0&amp;l=[{video_id:'b198756c208b6',logo_file:'https://supportforums.cisco.com/servlet/JiveServlet/downloadImage/6472/cisco_logo.png'}]" /></object></span>

ASA Pre-8.3 to 8.3 NAT configuration examples
<a href="https://supportforums.cisco.com/docs/DOC-9129">https://supportforums.cisco.com/docs/DOC-9129</a>

ASA NAT migration problems when upgrading to 8.3 ; Syslog "%ASA-5-305013: Asymmetric NAT rules matched for forward and reverse flows"
<a href="https://supportforums.cisco.com/docs/DOC-12569">https://supportforums.cisco.com/docs/DOC-12569</a>
