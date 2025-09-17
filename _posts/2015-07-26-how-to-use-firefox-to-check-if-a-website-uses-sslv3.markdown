---
layout: post
title: How to use Firefox to check if a website uses SSLv3
date: 2015-07-26 10:57
comments: true
categories:
- firefox
- ssl
- misc
---
We should all know by now that [SSLv3 is a broken security protocol due to the POODLE vulnerability](https://isc.sans.edu/forums/diary/SSLv3+POODLE+Vulnerability+Official+Release/18827/). Firefox tries to connect to a website using the strongest security that the web server provides. It is possible to change the settings in Firefox to force a less secure connection which can tell us whether SSLv3 is on or not.

### Change the settings
In firefox go to the url `about:config`. Then search for `tls.version`.

![](/images/ssl3-1.png)

The different TLS versions are:

* `0` - SSL 3.0
* `1` - TLS 1.0
* `2` - TLS 1.1
* `3` - TLS 1.2

By changing the max version to be `0` you are telling Firefox you don't want to use anything higher than SSLv3. Please only do this for testing purposes and do not leave your browser on this setting. 

### Testing the connection
Now browse to the site you wish to test. Click on the little padlock icon in the address bar, then click more information.

![](/images/ssl3-2.png)

At the bottom of this panel will display the technical details about the site. In this image below you can see that the browser negotiated to the site using SSLv3 which is broken encryption. 

![](/images/ssl3-3.png)


 
