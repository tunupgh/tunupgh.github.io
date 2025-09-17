---
layout: post
title: Cisco ASA Licensing Quick Reference Guide
date: 2013-07-01 11:26
comments: true
thumbnail: "/images/asalicensing.png"
cheatsheetname: Cisco ASA Licensing
categories:
- asa
- cisco
- cheatsheet
- scripts
- tips
- licensing
---
One of the most confusing things about Cisco ASA's is the licensing structure. While it is very nice to have a single train of OS files to deal with, it is incredibly hard to keep track of all of the licensing details regarding the ASA. Here is a handy guide that may help you wade through the piles of documentation around it.

## Quick Reference Guide

![ASA Licensing Guide IMG](/images/asalicensing.png)

Download: [PNG Image](/images/asalicensing.png), [PDF Document](/images/ASALicensingCheatSheet.pdf)


To see what licenses are installed on the ASA review the outputs of the `show version` command. The output will look something like this:

```
Inside Hosts                      : Unlimited      perpetual
Failover                          : Active/Active  perpetual
Encryption-DES                    : Enabled        perpetual
Encryption-3DES-AES               : Enabled        perpetual
Security Contexts                 : 2              perpetual
GTP/GPRS                          : Disabled       perpetual
AnyConnect Premium Peers          : 2              perpetual
AnyConnect Essentials             : Disabled       perpetual
Other VPN Peers                   : 250            perpetual
Total VPN Peers                   : 250            perpetual
Shared License                    : Disabled       perpetual
AnyConnect for Mobile             : Disabled       perpetual
AnyConnect for Cisco VPN Phone    : Disabled       perpetual
Advanced Endpoint Assessment      : Disabled       perpetual
UC Phone Proxy Sessions           : 2              perpetual
Total UC Proxy Sessions           : 2              perpetual
Botnet Traffic Filter             : Disabled       perpetual
Intercompany Media Engine         : Disabled       perpetual
IPS Module                        : Disabled       perpetual
Cluster                           : Disabled       perpetual
```
Now we will cover each of these items in detail.

## VLANs
On the Cisco ASA 5505 you may see a line stating:<br>
`VLANs                             : 3              DMZ Restricted`
On the 5505 each interface is assigned a VLAN. This is stating only 3 VLANs can be created which are then eligible to be applied to an interface. The "DMZ Restricted" term indicates a further restriction. Two VLANs can talk to each other without issues. Suppose these are "inside" and "outside". The 3rd VLAN we'll call "DMZ". The DMZ interface is restricted to only be able to pass traffic to either the inside interface or outside but cannot pass traffic to both. That means it's "DMZ Restricted". A sec plus license can be applied to remove this restriction.

## Inside Hosts
This is "Unlimited" in all models except for the 5505. The 5505 comes with 10 hosts standard and can be upgraded to 50 or Unlimited number of hosts. This specifically means the ASA will only build connections for 10 hosts within the network at a time. 

## Failover
This is not to be confused with "Clustering". Failover is when two firewalls (which are matching models and hardware) are paired together for redundancy. In an Active/Standby scenario, one firewall acts as the active one and accepts connections going through it. The active firewall will keep the active IP. So if the primary firewall goes down, the standby unit will kick over to active and even take over the active IP. This failover usually occurs without dropping a packet because the connection table is actually replicated between the two units.

An Active/Active scenario is generally used for multi context firewalls. This may be used when Primary firewall is the Active firewall for Context A, and the secondary fireall is the Active firewall for Context B. If one firewall goes down, the remaining one becomes Active for both Contexts. 

## Encryption
Encryption-DES comes standard on all firewalls.

Encryption-3DES-AES is a $0 cost license that enables 3DES and AES encryption methods.

## Security Contexts
By default the ASA has 2 contexts that can be ran simultaneously. This is not supported in the 5505 and requires the Security Plus license for 5510 and 5512-X.

A multi context firewall is one which runs multiple separate firewalls inside a single chassis. The admin context is used to determine which interfaces are assigned to which contexts.

## Other VPN Peers / Total VPN Peers
Included with base license. It is used for site to site IPSec tunnels using IKEv1 or IKEv2. It is also used when using remote access VPN (Legacy Cisco VPN Client) using IKEv1. IPSec remote access IKEv2 requires AnyConnect Essentials or AnyConnect Premium.

## AnyConnect
For more information regarding AnyConnect Premium and Essentials see my blog post [Understanding AnyConnect Licensing](http://www.tunnelsup.com/tup/2012/08/08/understanding-cisco-asa-anyconnect-licensing/).

Advanced Endpoint Assessment is used to enhance the host scan. When normally a host scan can detect when a VPN user is out of compliancy and not allowed conection into the network, the Advanced Endpoint Assessment can actually suggest to the user what they'll need to do to fix it. Either by downloading an update or guiding them on fixing a program.

AnyConnect for Cisco VPN Phone is used for allowing VOIP phones that have built in VPN support to VPN into the ASA and then contact the Call Manager. Only supported on CallManager 8.0+ and IP Phone firmware 9.x.

## Botnet
For more information on the botnet license and capability see my blog post [Understanding Botnet Licensing](http://www.tunnelsup.com/tup/2013/05/08/cisco-asa-botnet-license/).

## GTP/GPRS
This is used by ISPs who have 3G traffic going through their network.

## Shared License                    
The Shared SSL VPN license is a way to have a central ASA act as an AnyConnect premium peer license server and other participant ASA's can ask for licenses (in blocks of 50 at a time) from the shared license server. This requires both a Server license and particpant license.

## UC Phone Proxy
This is a legacy license because CallManager 8.0+ does not support this feature any longer.

## Intercompany Media Engine
Allows support to offload communications from PSTN to IP-based SIP trunks through the ASA. Primarily used for business to business federation between CUCM's. Requires version 8.3(1)+.

## IPS Module           
In ASA 5500-X series firewalls the IPS module is entirely software based and requires an additional license to enable it. Once the license is enabled for the software firewall, and additional support contract (smartnet) is required to update the IPS sensor with signatures.

## Cluster                           
This feature is new to 9.0(1). It allows multiple ASA's to be grouped into a single logical device. Clustering will probably never be supported on the ASA 5500 series models. It currently is only available on 5580 and 5585-X models. Future plans to support all 5500-X models. 

For further information read: [http://communities.cisco.com/docs/DOC-26234](http://communities.cisco.com/docs/DOC-26234)

## Security Plus License
Refer to the image below to understand what the Security Plus license does. Note: it is only available for the 5505, 5510, 5512-X and 5585-X.

![ASA Sec Plus IMG](/images/asasecplus.png)


## Further Reading
For more information review Cisco documentation: [http://www.cisco.com/en/US/docs/security/asa/asa91/license/license_management/license.html](http://www.cisco.com/en/US/docs/security/asa/asa91/license/license_management/license.html)


