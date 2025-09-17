---
layout: post
title: How to Release Subscriptions on a Cisco IPS
date: 2013-06-11 19:53
comments: true
categories:
- cisco
- troubleshooting
- ips
- ids
- intrusion prevension
- sdee
---
The Cisco IPS (Intrusion Prevension System) can handle up to 5 SDEE connections and sometimes holds on to old ones or gets stuck. It is possible to clear these connections out.

### Find the Subscription ID
SSH into the IPS with admin role. Issue the following command to determine the subscription ID and status.

```
IPS-Sensor# show stat sdee-server
General
     Open Subscriptions = 1
     Blocked Subscriptions = 0
     Maximum Available Subscriptions = 5
     Maximum Events Per Retrieval = 500
Subscriptions
     sub-58-270c3ecd
          State = Open
```

### Clear the Session
In order to release the session you will need a web browser that can get to the IPS device.

Go to:<br>
`https://<IP Address>/cgi-bin/sdee-server?action=close&subscriptionId=<Session ID>`

For Example from above:<br>
`https://10.10.10.10/cgi-bin/sdee-server?action=close&subscriptionId=sub-58-270c3ecd`

You will be asked to login to the device. You will not receive a valid website. You will be presented with:

```
   <?xml version="1.0" encoding="UTF-8" ?>
- <env:Envelope xmlns="http://www.cisco.com/cids/2003/09/cidee" xmlns:env="http://www.w3.org/2003/05/soap-envelope" xmlns:sd="http://example.org/2003/08/sdee" xmlns:cid="http://www.cisco.com/cids/2003/09/cidee">
     - <env:Header>
          - <sd:oobInfo>
               <sd:sessionId>15c8dde7c627b79997ab446fd71ef2d6</sd:sessionId>
            </sd:oobInfo>
       </env:Header>
       <env:Body />
   </env:Envelope>
```


### Verify Session is Closed
Verify session has been closed by SSH into the IPS and issuing the command:

```
IPS-Sensor# show stat sdee-server
General
     Open Subscriptions = 0
     Blocked Subscriptions = 0
     Maximum Available Subscriptions = 5
     Maximum Events Per Retrieval = 500
```
