---
layout: post
title: 8 Ways to Defend Against Denial of Service Attacks
date: 2016-05-02 15:48
comments: true
categories:
- defsec
- infosec
- dos
- attacks
---
Protecting against Denial of Service attacks is difficult and takes numerous steps to protect the network. Unfortunately, even with all these steps it's still possibly to have a DoS scenario in the network. A large DoS attack is not that hard technically or economically to conduct. 

First get to know the [different types of DoS attacks](/different-types-of-dos-attacks/).







## 1. Be Nice
If you're a company that is doing things that makes people angry, you'll be hit by DoS attacks much more frequently than a company that isn't a political lightning rod. Inform your leaders that if you do a specific lawsuit or make a specific stance on something that would elevate the risk of the network and these must be taken into consideration.

In short, don't give someone a reason to DoS you.

## 2. Use a CDN for websites

A content delivery network will offload traffic from your network to the CDN's network. The CDN provider will cache your website and serve any static content it can on it's website for you. This will result in a lot less traffic going to your site. The purpose of a CDN is to both serve your customers quicker by having web servers closer to them, and to help mitigate against DoS attacks since an attack would hit only their website and never yours.

Major CDN providers out there are: Cloudflare, Akamai, and Limelight. 

## 3. Tune your webserver/application
By default, Apache has a low number of max connections allowed. You'll want to check all of its settings to make sure it's tuned correctly to your environment. If you want Apache to accept thousands of connections, you'll need a lot of RAM etc. Knowing exactly what these limitations are will help you troubleshoot issues when you are under attack and also be able to let leadership know what you're capable of sustaining. Other applications may have tuning capabilities for number of connections also.



## 4. Tune your edge device
Whether you have a router or firewall on your edge (between your network and the internet), you should tune this to only permit the number of connections your web server or application can permit. The reason for this is because if you've tuned your web server to only accept conns for the resources it has, sending it more than the max can knock over the server. A firewall is also more efficient and better at blocking traffic than a web server. Get these two devices working in tandem for extra efficiency.

The goal in tuning a firewall is to permit only traffic needed and everything else is blocked. If you have a Cisco ASA on your edge check out this post on [Cisco ASA best practices](/cisco-asa-best-practices/).

## 5. Use a load balancer
A load balancer can offload traffic in ways that are very helpful. It can do many things but the ones that help us here are:

* Spread traffic to a farm of web servers
* Completes the TCP handshake before forwarding the traffic to prevent against half open connection overload.
* Can be an SSL termination point to offload encryption computations
* Conduct rate limiting

## 6. Server hardening
Besides tuning the application server for connections you'll want to harden it in other ways. Here's some ideas:

* Use IPTables to permit only the traffic you expect
* Uninstall services and applications on the server to make it utilize resources more efficiently
* Configure server to auto recover on system failures. Such as reboot if there's a kernel panic.
* Harden the server to be more resilient.

Here's some links to help you get started hardening a linux server:

* http://www.enigma.id.au/linux_tuning.txt
* http://www.securityfocus.com/infocus/1729
* http://fasterdata.es.net/TCP-tuning/linux.html
* http://fedorahosted.org/ktune/browser/sysctl.ktune
* http://www.cymru.com/Documents/ip-stack-tuning.html
* http://www.kernel.org/doc/Documentation/networking/ip-sysctl.txt
* http://www.frozentux.net/ipsysctl-tutorial/chunkyhtml/index.html
* http://knol.google.com/k/linux-performance-tuning-and-measurement
* http://www.cyberciti.biz/faq/linux-kernel-tuning-virtual-memory-subsystem/
* http://www.redbooks.ibm.com/abstracts/REDP4285.html
* http://www.speedguide.net/read_articles.php?id=121
* http://lartc.org/howto/lartc.kernel.obscure.html


## 7. Review Your Analytics and Logs
You should know what your normal traffic looks like and keep an eye on usage and statistics. I've seen websites gain popularity and incur a DoS simply because they didn't know they were getting that amount of traffic normally. Knowing what's on your network helps troubleshooting tremendously. 

If you're seeing attackers hitting your network with malicious intent, use a firewall to block their connections into your network entirely. Review logs frequently and make adjustments as needed to keep out traffic you don't need. 


## 8. Patch Applications
Applications have bugs. Sometimes these bugs cause the application to crash. If the developers know about the problem they will fix it in the code and release updates on the applications. It's important to keep any internet facing products and applications fully patched so that attackers have less of a chance of finding a bug that causes your application to crash.







