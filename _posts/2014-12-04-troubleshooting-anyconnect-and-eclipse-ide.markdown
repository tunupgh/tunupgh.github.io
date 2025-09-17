---
layout: post
title: Troubleshooting AnyConnect and Eclipse IDE
date: 2014-12-04 13:36
comments: true
categories:
- troubleshooting
- eclipse
- anyconnect
- cisco
---
Eclipse is an IDE used to build Android applications. One of our developers was trying to use the CVS connection within Eclipse to push their code to a server over the VPN. However this was failing.

## Problem
Cannot connect to CVS in Eclipse when on Cisco AnyConnect VPN.

The developer's workflow was this:

1.  Logging into AnyConnect VPN
1.  Going into Eclipse IDE
1.  Connecting to CVS from within Eclipse IDE via the CVS plugin
 
The CVS server wouldn't connect. But if the developer would try connecting to CVS via windows explorer (not within the IDE) while on the VPN is DOES work.

## Resolution
Cisco AnyConnect isn't the problem. It's a setting within Eclipse that must be changed.

Try adding the following to `eclipse.ini`:

```
openFile
-vmargs
-Djava.net.preferIPv4Stack=true
```

Then restart Eclipse and try again.


#### Small rant

I HATE being a firewall admin when I have to troubleshoot individual applications. I worked on this for a week and was officially stumped and told the user to find another way. But then I found this on a forum and it worked for them. Cisco, get your act together with AnyConnect. Make it work! I think it's your fault because these problems didn't exist on the old legacy VPN client!
