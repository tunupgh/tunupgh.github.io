---
layout: post
title: Sample Juniper JunOS Config for SRX210
date: 2016-04-16 23:23
comments: true
categories:
- juniper
- srx
---
Need a quick template to get you started deploying a Juniper SRX 210? These are usually firewalls that go into a small office or home office. In our configuration below, it is plugging into a cable modem, and has no DMZ or static NATs.

```
set system host-name MY-SRX210
set system name-server 8.8.8.8
set system services ssh
set system services web-management http
set system services web-management https system-generated-certificate
set system services web-management https interface ge-0/0/0.0
set system services dhcp router 192.168.200.2
set system services dhcp router 192.168.201.1
set system services dhcp pool 192.168.201.0/24 address-range low 192.168.201.33
set system services dhcp pool 192.168.201.0/24 address-range high 192.168.201.95
set system services dhcp propagate-settings ge-0/0/0.0
set system syslog archive size 100k
set system syslog archive files 3
set system syslog file messages any critical
set system syslog file messages authorization info
set system syslog file interactive-commands interactive-commands error
set system max-configurations-on-flash 5
set system max-configuration-rollbacks 5
set system license autoupdate url https://ae1.juniper.net/junos/key_retrieval
set interfaces ge-0/0/0 description OUTSIDE
set interfaces ge-0/0/0 unit 0 family inet dhcp
set interfaces ge-0/0/1 description INSIDE
set interfaces ge-0/0/1 unit 0 family inet address 192.168.100.1/24

set routing-options static route 192.168.0.0/16 next-hop 192.168.100.2
set routing-options static route 0.0.0.0/0 next-hop 44.44.44.44
set security nat source rule-set NAT-INSIDE from zone INSIDE
set security nat source rule-set NAT-INSIDE to zone OUTSIDE
set security nat source rule-set NAT-INSIDE rule RULE1 match source-address 192.168.0.0/16
set security nat source rule-set NAT-INSIDE rule RULE1 match destination-address 0.0.0.0/0
set security nat source rule-set NAT-INSIDE rule RULE1 then source-nat interface
set security screen ids-option SCREEN-OUTSIDE icmp ping-death
set security screen ids-option SCREEN-OUTSIDE ip source-route-option
set security screen ids-option SCREEN-OUTSIDE ip tear-drop
set security screen ids-option SCREEN-OUTSIDE tcp syn-flood alarm-threshold 1024
set security screen ids-option SCREEN-OUTSIDE tcp syn-flood attack-threshold 200
set security screen ids-option SCREEN-OUTSIDE tcp syn-flood source-threshold 1024
set security screen ids-option SCREEN-OUTSIDE tcp syn-flood destination-threshold 2048
set security screen ids-option SCREEN-OUTSIDE tcp syn-flood timeout 20
set security screen ids-option SCREEN-OUTSIDE tcp land
set security zones security-zone OUTSIDE screen SCREEN-OUTSIDE
set security zones security-zone OUTSIDE interfaces ge-0/0/0.0 host-inbound-traffic system-services dhcp
set security zones security-zone INSIDE address-book address MGT-PC 192.168.0.0/24
set security zones security-zone INSIDE address-book address NET-LOCAL 192.168.0.0/16
set security zones security-zone INSIDE host-inbound-traffic system-services all
set security zones security-zone INSIDE host-inbound-traffic protocols all
set security zones security-zone INSIDE interfaces ge-0/0/1.0 host-inbound-traffic system-services snmp
set security zones security-zone INSIDE interfaces ge-0/0/1.0 host-inbound-traffic system-services all

set security policies from-zone INSIDE to-zone OUTSIDE policy POL-INSIDE-TO-OUTSIDE match source-address any
set security policies from-zone INSIDE to-zone OUTSIDE policy POL-INSIDE-TO-OUTSIDE match destination-address any
set security policies from-zone INSIDE to-zone OUTSIDE policy POL-INSIDE-TO-OUTSIDE match application any
set security policies from-zone INSIDE to-zone OUTSIDE policy POL-INSIDE-TO-OUTSIDE then permit
set security policies from-zone INSIDE to-zone OUTSIDE policy POL-INSIDE-TO-OUTSIDE then count

set security policies from-zone INSIDE to-zone OUTSIDE policy P-DENY-ALL match source-address any
set security policies from-zone INSIDE to-zone OUTSIDE policy P-DENY-ALL match destination-address any
set security policies from-zone INSIDE to-zone OUTSIDE policy P-DENY-ALL match application any
set security policies from-zone INSIDE to-zone OUTSIDE policy P-DENY-ALL then deny
set security policies from-zone INSIDE to-zone OUTSIDE policy P-DENY-ALL then log session-init
set security policies from-zone INSIDE to-zone OUTSIDE policy P-DENY-ALL then count
set security flow tcp-mss ipsec-vpn mss 1350
set applications application-set MGT-SERVICES application junos-https
set applications application-set MGT-SERVICES application junos-ssh
```

### Quick start checklist

Here's is the bare minimum you need to get an SRX set up.

1. Configure the interfaces.
2. Configure a NAT or PAT for all addresses to be NAT'd to the outside or untrust interface.
3. Create a policy to only permit what is required and deny everything else.
4. Create any users and ssh access to the system.
5. Configure any routes that are required.
6. (Optional) enable the screen commands to enable firewall inspection.
7. (Optional) configure the SRX to act as a DHCP server to give out addresses.

