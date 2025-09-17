---
layout: post
title: Firefox - Secure Connection Failed - ssl_error_weak_server_ephemeral_dh_key
date: 2015-07-26 11:27
comments: true
categories:
- firefox
- misc
---
Have you seen the following error?

> **Secure Connection Failed**

> An error occurred during a connection to example.com. SSL received a weak ephemeral Diffie-Hellman key in Server Key Exchange handshake message. (Error code: ssl_error_weak_server_ephemeral_dh_key)

> * The page you are trying to view cannot be shown because then authenticity of the received data could not be verified.

> * Please contact the website owners to inform them of this problem.

![](/images/dhkey1.png)

You get this error when the server you are trying to connect to is vulnerable to the [logjam](https://weakdh.org/) vulnerability. You should report this problem to the server owner so they can fix it immediately. This server is considered unsafe and is vulnerable to man-in-the-middle attacks.

Newer versions of Firefox and Chrome will block connections to unsafe servers by default. 

### Bypassing the weak security
If you really wish to access the site anyway you can go to `about:config` in the address bar of Firefox. Then search for `security.ssl3.dhe`. Then change this value to false.

![](/images/dhkey2.png)

After that, your page should load. Please change the setting in your browser back to true when you are done so your browser is not vulnerable to logjam and man-in-the-middle attacks.