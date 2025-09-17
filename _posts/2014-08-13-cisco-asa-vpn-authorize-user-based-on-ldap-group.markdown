---
layout: post
title: Cisco ASA VPN - Authorize user based on LDAP group
date: 2014-08-13 21:00
comments: true
categories:
- cisco
- asa
- ldap
- authorization
- scripts
---
It is possible to authenticate to LDAP but then only allow a user in if they are in the right LDAP group. This post will explain how to authorize a
user based on their LDAP group they are a member of.

The theory for this task is to set a default group policy which has no access to the network, then create an LDAP map that maps a LDAP group to a group-policy.


### Configure LDAP authentication

First what you'll need to do is make sure you have LDAP authentication working. Create a aaa-server that uses the LDAP protocol. The configuration may look like this:

```
aaa-server LDAP protocol ldap
aaa-server LDAP (INSIDE) host 10.10.10.1
 ldap-base-dn DC=example,DC=com
 ldap-scope subtree
 ldap-naming-attribute sAMAccountName
 ldap-login-password *****
 ldap-login-dn CN=svc_asavpn,OU=users,OU=chi,DC=example,DC=com
```

The `ldap-base-dn` will be where where the ASA starts looking for an authenticated user. I recommend setting this as the first level of your AD tree.

The `ldap-scope subtree` tells LDAP to look for this user in any subtree. The other option is just a single subtree up.

The next 3 commands are setting up the LDAP user that will be used to bind to LDAP. The `ldap-naming-attribute` command says we'll be using the sAMAccountName as the identifier of our login name. The `ldap-login-password` is the LDAP password for the user we are using to bind to LDAP. In this case the user is svc_asavpn. The `ldap-login-dn` tells LDAP where that user lives. It's important to know the full path of that user as indicated in the commands above.

Once you have those commands in the easiest way to test the connectivity is to use the `test` command. It looks something like this:

```
CHIASA# test aaa-server authentication LDAP username svc_asavpn password p@ssw0rd
Server IP Address or name: 10.10.10.1
INFO: Authentication Successful
```

It's important that this test is successful. Failure to get LDAP working successfully here will result in harder to troubleshoot problems later.

You may be able to get additional help by turning on `debug ldap 255` then trying the test again.


### Create Group-Policies for the VPN

Create two group policies. One that has no access and denies the user from logging in, and one that has the correct permissions to allow a user to login.


First create the group for no access

```
group-policy NoAccess internal
group-policy NoAccess attributes
 vpn-simultaneous-logins 0
```

We just want a group policy that doesn't allow anyone to login so if we restrict the login count to 0 nobody will ever be able to login.


Next create the group policy that will allow the user access.

```
group-policy GRPPOL-RA-VPN internal
group-policy GRPPOL-RA-VPN attributes
 dns-server value 10.10.10.1
 vpn-simultaneous-logins 3
 vpn-tunnel-protocol ssl-client
```

Note that sometimes we need to explicitly say the max simultaneous logins is more than 0 since we made it 0 earlier it may get inherited.


### Create the Tunnel Group

```
tunnel-group GRP-RA-VPN type remote-access
tunnel-group GRP-RA-VPN general-attributes
 address-pool POOL-RA-VPN
 authentication-server-group LDAP
 default-group-policy NoAccess
```

We've set the authentication group to be the aaa LDAP server we configured in step one. Also take note we've set the default group policy to be the NoAccess one.


### Create an LDAP map

An LDAP map will look at the LDAP settings that user has and if there's a match will assign them to a specific group-policy (amongst other options). Essentially we are saying deny all users from VPN access, unless they are a memberOf the specified group and if so assign them to a different group-policy.

```
ldap attribute-map MAP-ANYCONNECT-LOGIN
  map-name  memberOf Group-Policy
  map-value memberOf CN=vpn_users,OU=groups,OU=chi,DC=example,DC=com GRPPOL-RA-VPN
```

Since these are seldom used commands lets break them down all the way.

`memberOf` is the specific LDAP flag we are going to be looking for. We want to check if this user is a memberOf a group.

`Group-Policy` says that if there's a match, lets assign them a new group-policy. In older version of ASA (<8.2.5) use this instead: IETF-Radius-Class.

`CN=vpn_users,OU=groups,OU=chi,DC=example,DC=com` is the location of the group in AD to check if the user is a memberOf.

`GRPPOL-RA-VPN` is the name of the group-policy we will assign them to if there is a match.

`map-name` is more like indicating the type of map attribute to check against and what will be assigned if there's a match.

`map-value` defines the values to be checking against and assigning to. You can have multiple map-value commands in one LDAP map.


Now apply the LDAP map.

```
aaa-server LDAP (INSIDE) host 10.10.10.1
 ldap-attribute-map MAP-ANYCONNECT-LOGIN
```


### Test to verify it's working

You can try to login to the VPN using an LDAP account or you can try the `test` command that was talked about earlier. If you have `debug ldap 255` on you'll see the following in a successfully authenticated message:

```
[-2147483628]   memberOf: value = CN=vpn_users,OU=groups,OU=chi,DC=example,DC=com
[-2147483628]           mapped to Group-Policy: value = GRPPOL-RA-VPN
[-2147483628]           mapped to LDAP-Class: value = GRPPOL-RA-VPN
```

If you're seeing that message it means the user was given the proper group-policy to login with. You can also verify the test by successfully logging in via a VPN session and check if the user has the right group-policy when looking at the user doing `show vpn-sessiondb anyconnect`. 