---
layout: post
title: Cisco ASA API
date: 2015-11-11 21:55
comments: true
categories:
- cisco
- asa
- api
- scripts
---
Requires ASA 9.3(1) or higher which requires the 5500-X line or ASAv.

### Initial Configuration
Download the `.spa` file from cisco.com. Place it on the ASA. Then enable ASDM and the rest-api.

```
aaa authentication http console TACACS+ LOCAL
http server enable
http 192.168.1.0 255.255.255.0 INSIDE
rest-api image disk0:/asa-restapi-111-lfbff-k8.SPA
rest-api agent
```

### Documentation

You can view documentation of the API and test that the API is working by going to the following URL (replace the IP with your ASA IP).

`https://192.168.1.1/doc`

To properly view the documentation, your computer needs access to the internet to download specific resources like jquery. 