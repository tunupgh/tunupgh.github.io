---
layout: post
title: SSL VPN access is not allowed
date: 2013-06-06 22:17
comments: true
categories:
- ssl
- vpn
- cisco
- asa
- troubleshooting
- webvpn
- clientless
- anyconnect
- licensing
---
Recently I tried installing WebVPN usability onto a Cisco ASA firewall. When trying to login I received the following error:<br>
![SSL VPN Error IMG](/images/sslvpnerror.png)
> Clientless (browser) SSL VPN access is not allowed

That's odd. I'm the administrator. I WANT to allow this. It's my goal to allow this! Also, I wish Cisco would have consistency for this type of VPN. Is it "Clientless (browser) SSL VPN" or "WebVPN" or "SSL VPN" or "Clientless VPN"? Make up your mind! 

Perhaps the logs give us more of a clue. It said:

> May 22 2013 17:20:42: %ASA-4-722050: Group <GP-WEBVPN> User <test-user> IP <198.198.198.198> Session terminated: SVC not enabled for the user

This immediately became a battle with licensing. A quick show version gives me the following details:

```
Licensed features for this platform:
Maximum Physical Interfaces       : Unlimited      perpetual
Maximum VLANs                     : 100            perpetual
Inside Hosts                      : Unlimited      perpetual
Failover                          : Active/Active  perpetual
Encryption-DES                    : Enabled        perpetual
Encryption-3DES-AES               : Enabled        perpetual
Security Contexts                 : 2              perpetual
GTP/GPRS                          : Disabled       perpetual
AnyConnect Premium Peers          : 50             perpetual
AnyConnect Essentials             : 250            perpetual
Other VPN Peers                   : 250            perpetual
Total VPN Peers                   : 250            perpetual
Shared License                    : Disabled       perpetual
AnyConnect for Mobile             : Enabled        perpetual
AnyConnect for Cisco VPN Phone    : Enabled        perpetual
Advanced Endpoint Assessment      : Disabled       perpetual
UC Phone Proxy Sessions           : 24             perpetual
Total UC Proxy Sessions           : 24             perpetual
Botnet Traffic Filter             : Disabled       perpetual
Intercompany Media Engine         : Disabled       perpetual
Cluster                           : Disabled       perpetual
```

It appears we have AnyConnect Premium Peer license applied to this firewall. But is it in use?
 
```
denver-5520/pri/act(config)# sh run webvpn
webvpn
 enable OUTSIDE
 anyconnect-essentials
 anyconnect image disk1:/anyconnect-win-3.0.0629-k9.pkg 1
 anyconnect enable
 tunnel-group-list enable
```
Doh! It appears `anyconnect-essentials` is enabled! Here's the reason why this is a problem.
 
Anyconnect-essentials does NOT support WebVPN. You must have Anyconnect Premium enabled to use WebVPN. It's confusing because the way the licensing works is that You can either have essentials on or not. If you don't then you have premium on. Either way you cannot have both at the same time. In order to make this WebVPN script work I had to say `no anyconnect-essentials` which enables the premium peers. This brought us from 250 possible simultaneous VPN sessions down to 50. 

To learn more about AnyConnect license (and why anyconnect-essentials doesn't allow you to use WebVPN) see [my post](http://tunnelsup.com/tup/2012/08/08/understanding-cisco-asa-anyconnect-licensing) about it.
