---
layout: post
title: Cisco ASA Troubleshooting failover when failover is off
date: 2014-09-18 22:01
comments: true
categories:
- cisco
- asa
- failover
- troubleshooting
---
Sometimes two firewalls will be in failover pair but for some reason one or both will turn failover off. What happens to the firewalls in this situation? Do both go active? Does one stay in standby? Great questions!


## Reasons for failover to turn off on its own

The ASA may suddenly turn failover off. That is, the command 'no failover' will be executed automatically in some situations. Here are some example situations when that may happen:

* If the licenses do not match
* If the modules do not match

It's not clear what else will cause this since Cisco just documents what is required for failover to work. They don't explain what will happen if you don't meet those requirements. Will failover just fail or will the commands be removed?

Sample message when the license does not match and failover is turned off.

```
Mate's license (AnyConnect for Cisco VPN Phone Enabled) is not compatible with my license (AnyConnect for Cisco VPN Phone Disabled). Failover will be disabled.
```

## What will happen when failover is turned off

If you have a failover pair that is working correctly and you turn failover off 'no failover' on the active unit, the failover link will stop sending packets between the two firewalls. However, the states will stay the same. The active unit will remain active and the standby unit will remain in standby state. The standby unit will continue to be standby until a failover event takes place.

## Checking the state of failover when failover is turned off

If the firewall has failover commands and a history of being in a pair it can still have a state of being active or standby. There are two ways to check this.

The first way to check is to change the prompt. Go into config mode and do `prompt hostname priority state` (must be done from system context in a multi context firewall). This will change the prompt to look something like this:

Active:
 UK-ASA/sec/actNoFailover#

Standby:
 UK-ASA/pri/stbyNoFailover# 

Notice in the prompt it indicates whether the device is active or standby even with failover turned off.

The second method is to do the command `show failover`. This will give the following results:

Active:
Failover Off

Standby:
Failover Off (pseudo-Standby)

## What does it mean if the firewall is in pseudo-standby

Pseudo-standby means that failover is turned off but this firewall is still in standby mode. This means the firewall will take the standby IP addresses of all interfaces. So if an interface has a configuration like this:

```
interface GigabitEthernet0/3
 ip address 192.168.1.1 255.255.255.0 standby 192.168.1.2
```

Then the pseudo-standby ASA will have the IP of 192.168.1.2. You can check this by doing `show ip` and looking at the second section titled "Current IP Addresses".



## Conducting a failover event even with failover off

It's possible to switch the state of a firewall even when failover is turned off. Doing this is the same as doing it when it is turned on. The commands are:

`no failover active` will put the firewall in a standby state

`failover active` will put the firewall in an active state


If failover is turned off and the standby unit reboots **it will come back as active**.


## MAC address problems

In the following scenario there will be duplicate MAC addresses. Suppose there is a failover pair and the secondary unit is active when failover is turned off. This means the secondary unit has the MAC of the primary firewall and the primary has the mac of the secondary firewall. If the secondary unit reboots it will have no memory of what the MAC was for the primary unit and use it's own MAC address. This means that now both firewalls are using the same MAC address.

To fix this simply give one firewall a unique MAC to use. Do this by setting the MAC on the interface like this:

```
interface GigabitEthernet0/3
 mac-address 001c.59d3.f79b standby 001c.59d3.f77c
```

If the firewall is active it will use the first MAC and if the firewall is pseudo-standby it will use the second MAC.


## Turning failover back on

Upon bringing the two firewalls back into the failover group again it's important to do it in a deliberate way. You want the correct ASA and configuration to act as active and sync the config to the standby unit. You do not want a perhaps outdated config on an old ASA to push it's config to the ASA which has the newer config. You also don't want to trigger an unexpected reload.

Snippet from [Cisco website](http://www.cisco.com/c/en/us/td/docs/security/asa/asa82/configuration/guide/config/ha_active_standby.html#wp1056837):
> Configuration synchronization occurs when one or both devices in the failover pair boot. Configurations are always synchronized from the active unit to the standby unit. When the standby unit completes its initial startup, it clears its running configuration (except for the failover commands needed to communicate with the active unit), and the active unit sends its entire configuration to the standby unit.
>
> The active unit is determined by the following:
>
>* If a unit boots and detects a peer already running as active, it becomes the standby unit.
>
>* If a unit boots and does not detect a peer, it becomes the active unit.
>
>* If both units boot simultaneously, then the primary unit becomes the active unit, and the secondary unit becomes the standby unit.

If we have access to both firewalls then ideally we would want to enable the pair by doing the following:

1. On the one you want to be standby, verify it is in pseudo-standby mode and turn failover off `no failover`.
1. Verify no [MAC address conflicts](http://www.cisco.com/c/en/us/td/docs/security/asa/asa82/configuration/guide/config/ha_active_standby.html#wp1097271) will take place.
1. Connect any cables that need to be connected to the firewalls (failover, outside, inside, etc).
1. On the one you want to be active, verify it is in active mode and then enable failover.
1. Check the active unit by doing `show failover` to verify it's not failed. If it's in a failed state troubleshoot as needed to make it not failed.
1. On the standby unit enable failover.


Another way to force a unit to be standby during rejoining is to disconnect a cable or shutdown a monitored interface on the standby unit. This should make the unit in a failed state and therefor not try to become active.

