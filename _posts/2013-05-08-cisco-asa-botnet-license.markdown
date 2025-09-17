---
layout: post
title: Cisco ASA Botnet License
categories:
- asa
- blacklist
- botnet
- cisco
- dynamic-filter
- scripts
- licensing
- splunk
- whitelist
comments: true
---
Information regarding the Cisco ASA Botnet License.
## What is it?
Botnet Traffic Filter is an extra license that can be applied to a Cisco ASA firewall that provides detection and automatic blocking of known bots and botnets. The firewall grabs updates from Cisco's website to know which IPs to look for and block.

Besides stopping outside botnets from coming into the network the botnet filter is also very effective at identifying which hosts may have malicious software within the network. It will stop internal hosts from going out to a known botnet IP. It has the ability to listen for call-home or command/control behavior seen within the network.

## License
It is time based. The SKU is something like L-ASA55xx-BOT-1YR=

It costs between $200-$1500 per year. I will only fully work on version 8.2.2 and up.

Purchase license. Apply it using the activation-key command.

Requires the 3DES/AES license (this license is free but does not ship with an ASA).

## Initial Configuration
The firewall needs to communicate to update-manifests.ironport.com. This means it has to have a valid DNS lookup mechanism. To get this going do something like the following:

```
dns domain-lookup INSIDE
DNS server-group DefaultDNS
name-server 10.2.2.2
```

There are over 70,000 DNS names that are in the Cisco database and only 5000 IP addresses. To effectively look at each domain name, DNS snooping must be turned on. To do that issue the following commands:

```
class-map dynamic-filter_snoop_class
  match port udp eq domain

policy-map global_policy
  class dynamic-filter_snoop_class
	inspect dns preset_dns_map dynamic-filter-snoop
```

Next turn on the dynamic-filters. I like to start by blocking everything that is blacklisted and whitelist the CEO's IP from being blocked. Do this by issuing the following commands:

```
dynamic-filter updater-client enable
dynamic-filter use-database
dynamic-filter enable interface OUTSIDE
dynamic-filter drop blacklist interface OUTSIDE
dynamic-filter whitelist
address 10.1.4.4 255.255.255.255
```

## Checking to see what's happening
Verify the ASA is downloading the rules:

```
show dynamic-filter updater-client
```
<br />
Verify traffic is being inspected:

```
show dynamic-filter statistics
```

The show command above will tell you the `Total conns classified`. This number will only go up if traffic hits a whitelist blacklist or greylist. Cisco has a bugid CSCti70280 which is super confusing because it makes us think this isn't intended behavior.


<br />
View which hosts are being blocked:

```
show dynamic-filter reports infected-host all
```

<br />
A good test to conduct to see if your botnet is working is to look at the output of `show dynamic-filter data` and look for one of the hosts that's in the "sample contents from database". Try to get to one of those hosts on port 80 making sure your traffic will go through this firewall. You should then see your statistics going up and if you're blocking blacklisted hosts then your session should be blocked.

<br />


More information on Cisco's website:

[http://www.cisco.com/en/US/docs/security/asa/asa82/configuration/guide/conns_botnet.html#wp1129967](http://www.cisco.com/en/US/docs/security/asa/asa82/configuration/guide/conns_botnet.html#wp1129967)

