---
layout: post
title: WebVPN or SSL VPN on an ASA
categories:
- cisco
- scripts
- asa
- script
- ssl
- vpn
- webvpn
comments: true
---
![WebVPN diagram - IMG](/images/webvpn.jpg)

WebVPN (or often called SSL VPN) (or sometimes called clientless VPN) is used when someone needs to access a web based application that is on the private network. A web browser is used for all the encryption and authentication.

After applying the config below the web user should be able to point their browser to https://11.11.11.11 to access the device at 192.168.11.2.

### BLUE ASA

First make sure to have a SSL certificate on the ASA. This is needed because the ASA is acting as a web proxy and requires an SSL cert to be there to create the connection to the client.

```
! Generate a self signed certificate
crypto ca trustpoint SELF-SIGNED-CERTIFICATE
proxy-ldc-issuer
id-usage ssl-ipsec
no fqdn
subject-name CN=firewall-name
enrollment self
crypto ca enroll SELF-SIGNED-CERTIFICATE noconfirm
```

Configure the WebVPN

```
webvpn
  enable Outside
  ssl trust-point SELF-SIGNED-CERTIFICATE Outside
group-policy POL-SP-WEBVPN internal
group-policy POL-SP-WEBVPN attributes
  vpn-tunnel-protocol webvpn
  webvpn
   url-list none
tunnel-group WEBVPN-SHAREPOINT type remote-access
tunnel-group WEBVPN-SHAREPOINT general-attributes
  default-group-policy POL-SP-WEBVPN
```
Once the Group Policy and Tunnel Group are created go into ASDM to control the look and control what websites the user can access.