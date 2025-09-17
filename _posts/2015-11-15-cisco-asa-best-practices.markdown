---
layout: post
title: Cisco ASA Best Practices
date: 2015-11-15 20:45
comments: true
categories:
- cisco
- asa
- scripts
---
Here are a list of best practices that can be applied to a Cisco ASA.


## Keep it up to date

Upgrade the ASA version to stay on the latest maintenance release of your code. This way you stay ahead of any security issues or bugs that have been fixed in newer versions.

It's also a good idea to upgrade to stay ahead of any end of life code like version 8.2.

Upgrade major feature trains only when you need new features, or after train has matured.



## Enable unicast RPF

Unicast RPF guards against IP spoofing (a packet uses an incorrect source IP address to obscure its true source) by ensuring that all packets have a source IP address that matches the correct source interface according to the routing table.

This is disabled by default. It's a good idea to enable it on every interface like this.

```
ip verify reverse-path interface INSIDE
ip verify reverse-path interface OUTSIDE
ip verify reverse-path interface DMZ
ip verify reverse-path interface EXTRANET
```


## Set embryonic limits

An embryonic connection is also known as a half open connection. It means a SYN is a received, a SYN-ACK was sent back to the source, and we are waiting for the ACK back from the source. A lot of these indicates a DOS, or misconfiguration or another type of attack. Take a baseline of what is the normal amount of half open connections (`show conn` and look for aB, A, or SaAB connection flags) then double or triple that and make that your connection limit.

It's a good idea to set a limit for both incoming traffic to your servers, and outgoing traffic from your internal systems to the internet.

Sample code to permit only 100 embryonic connections to 192.168.1.50 on port 80. Also, this will only allow 25 connections per client to that host. Setting per-client-max is optional.

```
access-list ACL-MAX-CONNS extended permit tcp any host 192.168.1.50 eq www
!
class-map CLASS-PROTECT
 description Protect web server from attacks
 match access-list ACL-MAX-CONNS
!
policy-map POL-MAX-CONNS
 class CLASS-PROTECT
  set connection embryonic-conn-max 100 per-client-max 25
!
service-policy POL-MAX-CONNS interface outside
```


## Set maximum allowed connections

If you have something like a NAT in your firewall that goes to an Apache web server to give that web server a public IP, set a limit to that web server so it matches what Apache can accept. If the configuration in the web server says there's only 25 connections allowed at once, set that on the ASA so the web server can't get DoS'd.

Sample code for that may look like this below.

```
access-list ACL-MAX-CONNS extended permit tcp any host 192.168.1.50 eq www
!
class-map CLASS-PROTECT
 description Protect web server from attacks
 match access-list ACL-MAX-CONNS
!
policy-map POL-MAX-CONNS
 class CLASS-PROTECT
  set connection conn-max 25
!
service-policy POL-MAX-CONNS interface outside
```

## Enable logging

By having logging enabled, it really assists on troubleshooting issues. It's one of the first things I will look at when troubleshooting.

By having logging enabled to send to a remote syslog server helps a lot more because now logs will be stored there a lot longer than what the ASA can hold. Also, if the ASA reboots the logs it had before the reboot are gone. If there's an issue occurring you can look back in history of the logs to see how many times it's happened in the past.

Also, all config changes will trigger a syslog message. Having that record is very helpful for finding problems.

Sample code to enable logging to be seen by both the 'show logg' command and also to send all logs to 192.168.55.55 for remote storage.

```
logging enable
logging timestamp
logging buffer-size 128000
logging buffered notifications
logging trap notifications
logging host INSIDE 192.168.55.55
```

In the code above, only syslog level `notification` or above are being logged. But suppose you want get a certain log that's at `informational`. If you lower the log level to that, you'll get a lot more logs that may overwhelm your syslog server. Instead, find the syslog you want to raise the logging level and just adjust it to be at notification.

```
logging message 302014 level notifications
```

The config above will log traffic being permitted by the firewall.

Splunk is a sample tool that can monitor syslogs.

## Enable TACACS+ or RADIUS

If you use shared logins and you see a config change that wasn't authorized, it may be really hard to find out who made the change. Also, what happens when someone with that login leaves the company.

A better alternative is to use AAA commands to enable a RADIUS or TACACS+ servers for authentication. This will ultimately result in better user control of who can access the firewall and have better visibility into what people are doing on the firewall.


Here is some sample code.

```
aaa-server TACACS+ protocol tacacs+
 reactivation-mode depletion deadtime 30
aaa-server TACACS+ (INSIDE) host 192.168.66.66
 timeout 3
 key s3kr3tk3y

aaa authentication http console TACACS+ LOCAL
aaa authentication ssh console TACACS+ LOCAL
aaa authentication enable console TACACS+ LOCAL
```

With the code above, all authentication will go to the TACACS+ server first. If that is unavailable then the local accounts are used for authentication as a fall back.

It's also a good idea to audit the local accounts regularly to make sure there aren't an unexpected accounts created. Also, rotate the passwords on these fallback accounts regularly too.


## Understand what is normal traffic

Get to know what traffic is normal for your firewall. Do this so when there is an incident occurring or testing needs to be done, you have something to compare against.

Look at these areas to document what is normal traffic.

* Number of VPN connections and types of VPNs
* Number of TCP/UDP connections
* Number of active xlates
* Interface usage
* CPU and memory load


## Conduct SNMP monitoring

Have a server that you send SNMP traps to and have it also SNMP poll the ASA frequently. By monitoring this, you'll have a good understanding of when something is going wrong. Things that show up in SNMP logs are:

* Reboots
* Failovers
* High CPU
* High Memory
* Interface down
* or any syslog can be converted to an SNMP trap

Here is some sample code to enable SNMP:

```
snmp-server host INSIDE 172.16.1.18 poll community s3kr3tkee version 2c
snmp-server host INSIDE 172.16.1.18 trap community s3kr3tkee version 2c
snmp-server community s3kr3tkee
snmp-server enable traps snmp authentication linkup linkdown coldstart warmstart
snmp-server enable traps entity config-change fru-insert fru-remove
snmp-server enable traps memory-threshold
snmp-server enable traps interface-threshold
snmp-server enable traps connection-limit-reached
snmp-server enable traps cpu threshold rising
snmp-server enable traps nat packet-discard
```

MRTG is an example of a tool that can monitor this type of activity.


## Create a login banner

There is a cyberlegend that talks about a case where a hacker gained access to a router and because the banner said "Welcome to the system" the judge dismissed the case. While this is not a true story, there is still a good lesson here. Create a banner that states unauthorized access is not allowed.

Example banner:

```
banner motd *******************************************************************
banner motd THIS SYSTEM ACCESSES PROPRIETARY INFORMATION.  ACCESS IS RESTRICTED
banner motd TO AUTHORIZED USERS ONLY FOR LEGITIMATE BUSINESS PURPOSES.
banner motd UNAUTHORIZED ACCESS IS A VIOLATION OF STATE AND FEDERAL, CIVIL AND
banner motd CRIMINAL LAWS. ALL ACTIONS ARE BEING RECORDED.
banner motd PLEASE LOG OFF.
banner motd *******************************************************************
```

## Turn telnet off

Telnet is an insecure protocol. When you login to a device using telnet, your password goes over the wire in clear text for anyone to listen in on it. Use ssh instead. Never use telnet.

Conduct `show run telnet` and verify there are no subnets listed.

## Verify ssh and http subnets

In the ASA do a `show run ssh` and `show run http`. Look at the subnets that are listed. Verify those are what you wish to allow.

You would never want to see this on a firewall:

```
ssh 0.0.0.0 0.0.0.0 OUTSIDE
```

It essentially means anyone on any IP on the outside can ssh to this firewall. Lock down the firewall to only permit subnets that firewall admins are on.


## Permit as little as possible

One of the fundamental differences between a router and a firewall is that one wants to permit as much as possible and the other wants to deny as much as possible.

If you only have 5 valid subnets on the inside of your network, only permit those 5 subnets to pass through the firewall to the internet. This saves you from incidents where spoofed IPs inside the network cannot leave.

Restrict inbound connections to only the IPs that need inbound connections and only on the ports that are open. An example of this is a web server. Permit only inbound 80 and 443 for http and https. You don't want to permit ssh or telnet to that host or it will constantly be hit from the internet. Only permit what is needed and stop there.


## Remove unused configs

Review the firewall config each quarter and remove any configs that are no longer valid on your network. For instance if you decommissioned a subnet in your network, remove that subnet from the firewall. You only want to permit the traffic through your firewall that you know is valid.

Try the [Cisco ASA config cleanup tool](/config-cleanup/) here on TunnelsUp.

Each quarter, review the following configs to verify they are still valid

* ACLs
* NATs
* VPNs
* Objects/Object-groups

## Add an explicit deny any to all interface ACLs

At the end of an ACL is an implicit deny any any. So why explicitly add it?

* Lets you know how many hits are hitting it
* Avoids any vulnerabilities that might let someone bypass it
* Makes the ACL easier to read
	
	
	
## Reference

Here is a PDF of more best practices suggested by the NSA.

https://www.iad.gov/iad/customcf/openAttachment.cfm?FilePath=/iad/library/ia-guidance/security-configuration/networks/assets/public/upload/Cisco-ASA-Out-of-the-Box-Security-Configuration-Guide.pdf

<br><br>
Cisco Validated Design

https://cisco.com/go/cvd



