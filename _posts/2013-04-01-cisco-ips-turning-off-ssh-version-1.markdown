---
layout: post
title: Cisco IPS - Turning off ssh version 1
categories:
- cisco
- ips
- scripts
- ssh
- version 1
comments: true
---
Vulnerability scanners are reporting that ssh version 1 is on by default for Cisco IPS units. It is fairly simple to turn this off on the IPS if you have service level privilege.

First get into the service level account. This is essentially the linux shell under the IPS. 

`ssh service@x.x.x.x`

Once you are ssh'd in, issue these commands.

```
su
vi /etc/ssh/sshd_config
```

Change the line that says:

`#Protocol 2,1`

Uncomment it and take out the ',1' so it looks like this:

`Protocol 2`

Save the file and exit out of vi.

Restart the ssh daemon. (Warning: This may cause a failover if these are IPSMs in a failover pair)

`/etc/init.d/S60ssh restart`

Now you can test ssh'ing into your device and you should see this error:

```
[user@linuxbox1 ~]$ ssh 10.50.50.10 -1
Protocol major versions differ: 1 vs. 2
```

Voila, ssh version 1 is now disabled!