---
layout: post
title: Juniper SRX - ALG and Screen Options
date: 2017-03-03 20:12
comments: true
categories:
- juniper
- junos
- srx
---
What is ALG? What are screen options? When would I use this and why? This post will give you an introduction to these terms.

Screen Options and ALGs are two important security features of an SRX that detect and block abnormal traffic or attacks.

## ALG Application-Level Gateway

The application-level gateway (ALG) feature of Juniper SRX devices acts as a fixup to certain protocols that need help getting through the firewall. Since the firewall is stateful and restrictive by default, this causes certain protocols to not get through. Think about FTP, where the initial connection comes in on port 21 but then data is transmitted on port 20. Without ALG you would have to open port 20 and 21. With Juniper SRX you can configure a FTP ALG which will know that when a session is built on port 21, build the session for port 20 and 21. This way you only have to open port 21. This is similar to fixups or inspects on a Cisco ASA.

You can see which ALGs are available and enabled with this command:

```
srx> show security alg status
ALG Status :
  DNS      : Enabled
  FTP      : Enabled
  H323     : Enabled
  MGCP     : Enabled
  MSRPC    : Enabled
  PPTP     : Enabled
  RSH      : Enabled
  RTSP     : Enabled
  SCCP     : Enabled
  SIP      : Enabled
  SQL      : Enabled
  SUNRPC   : Enabled
  TALK     : Enabled
  TFTP     : Enabled
```

There are a few ALGs enabled by default. To see these (an configuration examples), use this hidden command:

`show configuration groups junos-defaults security alg`

To create new ALGs put them in the `edit security alg` section of the config.

To apply a new ALG, add it to the application in the `edit application application` section of the config.

Once the ALG is applied, adjust your policy to use that application. You can then check if your policy is using an ALG with this command:

`show security policies detail`

You can see if a session is being hit by an ALG with `show security flow session`, and if the flow is being impacted by an ALG it will be listed there.

To see a more detailed explanation of what each ALG does, check here: http://www.juniper.net/documentation/en_US/junos12.1x44/information-products/pathway-pages/security/security-algs-index.html

## Screen Options

Screen options are configurations applied to the firewall which act as a mini-IDS (intrusion detection system). The SRX can detect well established and known attacks such as DoS attacks, Teardrop attacks, fragmented ICMP packets, and more. There are about 30 screen options available. They are:

* **icmp/flood**                       Configure icmp flood ids option
* **icmp/fragment**                    Enable ICMP fragment ids option
* **icmp/ip-sweep**                    Configure ip sweep ids option
* **icmp/large**                       Enable large ICMP packet (size > 1024) ids option
* **icmp/ping-death**                  Enable ping of death ids option
* **ip/bad-option**                  Enable ip with bad option ids option
* **ip/block-frag**                  Enable ip fragment blocking ids option
* **ip/loose-source-route-option**    Enable ip with loose source route ids option
* **ip/record-route-option**         Enable ip with record route option ids option
* **ip/security-option**             Enable ip with security option ids option
* **ip/source-route-option**         Enable ip source route ids option
* **ip/spoofing**                    Enable IP address spoofing ids option
* **ip/stream-option**               Enable ip with stream option ids option
* **ip/strict-source-route-option**    Enable ip with strict source route ids option
* **ip/tear-drop**                   Enable tear drop ids option
* **ip/timestamp-option**            Enable ip with timestamp option ids option
* **ip/unknown-protocol**            Configure unknown protocol ids option
* **limit-session**                    Configure connection limits
* **tcp/fin-no-ack**                  Enable Fin bit with no ACK bit ids option
* **tcp/land**                        Enable land attack ids option
* **tcp/port-scan**                   Configure port scan ids option
* **tcp/syn-ack-ack-proxy**           Enable syn-ack-ack proxy ids option
* **tcp/syn-fin**                     Enable SYN and FIN bits set attack ids option
* **tcp/syn-flood**                   Enable SYN flood ids option
* **tcp/syn-frag**                    Enable SYN fragment ids option
* **tcp/tcp-no-flag**                 Enable TCP packet without flag ids option
* **tcp/winnuke**                     Enable winnuke attack ids option
* **udp/flood**                       Configure udp flood ids option

Some of these screen options are configured by default. To see which screen options are configured, look at the config here:

`show configuration security screen`

You can then see if the screen option is applied anywhere by doing:

`show security zones`

If it's applied you'll see it say **Screen:** and the screen policy name.

#### Configuring Screen Options

The best practice is to apply the screen to the least trusted zone. Often it is applied to the untrust zone. Traffic coming **in to** the zone with the screen applied, will be inspected by the screen options. Traffic going out of the zone will not be inspected by the screen.

Let's configure a new screen option to limit the number of connection to a server to only be **1** connection. To configure a new screen option and policy:

```
set security screen ids-option UNTRUST-SCREEN limit-session destination-ip-based 1
set security zones security-zone UNTRUST screen UNTRUST-SCREEN
```

This creates the screen policy called "UNTRUST-SCREEN" which says only allow one connection inbound through the screen.

Then the screen policy is applied to the UNTRUST interface.

Once this is committed you can check the screen status with this command:

`show security screen statistics zone UNTRUST`

To check logs you can look at the following output:

`show log messages | match RT_SCREEN`

Traffic that hits the screen will show up in the messages log with this string.
