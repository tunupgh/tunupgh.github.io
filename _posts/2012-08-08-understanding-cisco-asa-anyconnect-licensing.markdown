---
layout: post
title: Understanding Cisco ASA AnyConnect Licensing
categories:
- scripts
- anyconnect
- featured
- cisco
- asa
- essentials
- licencing
- premium
comments: true
---
This post will try to help understand the differences between anyconnect premium and anyconnect essentials licenses.

For a more complete understanding of all of the licensing on the Cisco ASA [see this post](/tup/2013/07/01/cisco-asa-licensing-quick-reference-guide).

<strong>Note: You cannot have both Essentials and Premium running at once.</strong>

<strong>Note: Cisco ASA 8.3+ no longer requires both the Active and Standby unit to each have a license. The active license is shared between the failover units. This should not be </strong><strong>confused with the 'shared premium license'.</strong>

<strong>Note: Cisco Secure Desktop is now deprecated. Cisco has stopped development for it.</strong>

![Anyconnect Outline - IMG](/images/anyconnect-licensing.png)

Source of this image: Cisco's Partner Education center - ASA Licensing Webex.
### To enable AnyConnect essentials:
Purchase the license (L-ASA-AC-E-55xx= it costs $100-$500).

Apply the license to the ASA using the `activation-key` command. This does not require a reboot.

Apply the config:

```
webvpn
  anyconnect-essentials
```

Now your firewall will be licensed to have up to however many connections that are on the "Total VPN Connections". For instance if your show version says this:

```
AnyConnect Premium Peers          : 2              perpetual
AnyConnect Essentials             : Enabled        perpetual
Other VPN Peers                   : 250            perpetual
Total VPN Peers                   : 250            perpetual
```

You will now be licensed to accommodate 250 anyconnect connectionns.

### To enable AnyConnect Premium
Buy the license. You must purchase a license for a specific number of users (L-ASA-SSL-10= costs around $800).

Apply the license to the ASA using the `activation-key` command. This does not require a reboot.

Configure the ASA:

```
webvnp
  no anyconnect-essentials
```

If you've already licensed this ASA for Essentials in the past then it will still show as an enabled license.

Once this is complete your ASA will be licensed to accept however many Anyconnect connections as you have Premium Licenses for. So if your `show version` looks like this:

```
AnyConnect Premium Peers          : 10             perpetual
AnyConnect Essentials             : Disabled       perpetual
Other VPN Peers                   : 250            perpetual
Total VPN Peers                   : 250            perpetual
```

Then your ASA can have 10 Anyconnect or webvpn users at once.

Note: The name "Anyconnect Premium" has changed a lot in different versions. Here are the different naming schemes.

* 7.1(1) known as "ssl vpn"
* 8.2(1) name changed to "anyconnect premium ssl vpn edition"
* 8.3(1) name changed to "anyconnect premium ssl vpn"
* 8.4(1) name changed to "anyconnect premium"

### AnyConnect for Mobile
This license allows AnyConnect connections from mobile devices. There is current support for iPhone, iPad, Android version 4.0 and up, rooted Androids and Samsung Galaxy's.

The mobile license is on or off and not tied to a number of users. It costs between $100-$500.

This license is applied by simply using the `activation-key` command. A reboot is not needed. There is no further configuration needed after that.

### Advanced Endpoint Assessment
Advanced Endpoint Assessment includes all of the Endpoint Assessment features, and lets you configure an attempt to update noncompliant computers to meet version requirements.

This license is applied by simply using the `activation-key` command. A reboot is not needed.

### Shared Premium License
New to ASA 8.3+ code is the ability to share licensing. This is only for Anyconnect Premium. It allows for one ASA to have a shared license which other ASAs can use.

This configuration requires two extra licenses. A license is needed for the shared server which indicates how many shared licenses there are and there also is a need for any participating ASAs.

After buying a shared <strong>participant</strong> license and applying it with the `activation-key` command, configure it with a command similar to this:

`license-server address 10.15.0.15 secret SeKreTkey`

The `show version` on the participant ASA will show this:

```
AnyConnect Premium Peers          : 2              perpetual
AnyConnect Essentials             : Disabled       perpetual
Other VPN Peers                   : 5000           perpetual
Total VPN Peers                   : 5000           perpetual
Shared License                    : Enabled        perpetual
```

Now buy the shared premium license for the server for the amount of users you wish to have.

Apply the license using the `activation-key` command. Then apply the following config:

```
license-server secret SeKreTkey
 license-server enable inside
```

The `show version` at this point looks like this:

```
AnyConnect Premium Peers          : 2              perpetual
AnyConnect Essentials             : Disabled       perpetual
Other VPN Peers                   : 5000           perpetual
Total VPN Peers                   : 5000           perpetual
Shared License                    : Enabled        perpetual
```

Also you can see the `show shared license` output:

```
Shared license utilization:
  AnyConnect Premium:
    Total for network :     5000
    Available         :     4900
    Utilized          :      100
  This device:
    Platform limit    :     5000
    Current usage     :       50
    High usage        :      100
  Messages Tx/Rx/Error:
    Registration    : 441798 / 441789 / 9
    Get             : 28 / 28 / 0
    Release         : 27 / 27 / 0
    Transfer        : 0 / 0 / 0

  Client ID           Usage   Hostname
  JMX1111             50      vpn-asa-01
```

If the license count isn't going up when joining a shared pool here are a couple of other settings that may help you:

```
vpn-sessiondb max-other-vpn-limit 5000
vpn-sessiondb max-anyconnect-premium-or-essentials-limit 500
```
These two settings will limit how many sessions this host or the other hosts can use. This may be set already and limiting what is usable.



