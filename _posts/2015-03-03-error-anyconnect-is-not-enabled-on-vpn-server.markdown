---
layout: post
title: 'Error: AnyConnect is not enabled on VPN server'
date: 2015-03-03 18:14
comments: true
categories:
- troubleshooting
- cisco
- asa
- anyconnect
---
Problem: When a user tries to connect to a Cisco ASA using the Cisco AnyConnect client the following error appears:

**"AnyConnect is not enabled on the VPN Server"**

Furthermore the logs of the ASA are saying something like this:

**%ASA-4-722050: Group <GRPPOL-AC-FULL> User <testuser> IP <111.111.111.111> Session terminated: SVC not enabled for the user**


### Cause

This message will appear if the group-policy that the user is connecting to doesn't have a `vpn-tunnel-protocol` for AnyConnect. Find the group-policy that user is connecting to. You can see which group-policy the user is using by looking at the logs.

Now check that the `vpn-tunnel-protocol` is configured.


ASA version > 8.3
```
group-policy GRPPOL-AC-FULL attributes
 vpn-tunnel-protocol ssl-client 
```

ASA version < 8.3
```
group-policy GRPPOL-AC-FULL attributes
 vpn-tunnel-protocol svc
```

#### Further troubleshooting

Is your user still not getting connected? Is it just happening to this user but other users are able to connect just fine? Whenever I have that problem it's likely due to the user not being in the right Active Directory group. Some firewalls are configured for authentication against AD through an ACS or RADIUS server. The ACS or Radius server can then be configured for assigning the group policy to the user after they have authenticated. If no group policy is assigned then there isn't going to be a `vpn-tunnel-protocol` assigned to that user either!