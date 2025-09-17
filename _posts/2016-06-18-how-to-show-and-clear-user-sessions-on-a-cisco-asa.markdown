---
layout: post
title: How to show and clear user sessions on a Cisco ASA
date: 2016-06-18 16:36
comments: true
categories:
- cisco
- asa
- troubleshooting
---
Sometimes you need to disconnect someone's ssh session to a Cisco ASA. This may be needed because users haven't logged out properly and have taken up all the sessions allowed.


## Check Usage Limits

You can check usage limits by seeing how many sessions the ASA *thinks* are connected.

```
FWL1# show resource usage resource ssh
Resource                 Current        Peak      Limit        Denied Context
SSH Server                     5           5          5           109 System
```

In this case the ASA can only connect 5 years and it thinks there are 5 sessions open and therefore it cannot connect any more.  

There's a bug that doesn't properly release sessions from the ASA. https://bst.cloudapps.cisco.com/bugsearch/bug/CSCsm68097


## Show current ssh sessions
To display all ssh sessions connected run this command.

```
FWL1# show ssh sessions                        
SID Client IP       Version Mode Encryption Hmac     State            Username
0   10.1.1.21       2.0     IN   aes128-ctr sha1     SessionStarted   james
                            OUT  aes128-ctr sha1     SessionStarted   james
2   10.1.1.22       2.0     IN   aes128-ctr sha1     SessionStarted   frank
                            OUT  aes128-ctr sha1     SessionStarted   frank
3   10.1.1.20       2.0     IN   aes128-ctr sha1     SessionStarted   henry
                            OUT  aes128-ctr sha1     SessionStarted   henry

```

## Disconnect user

If you want to disconnect user henry, find the SID and use this command:


```
FWL1# ssh disconnect 3
```
