---
layout: post
title: First Steps to Securing a Network
date: 2015-11-22 23:38
comments: true
categories:
- infosec
---
The first step in securing a network is creating an inventory of what is in the network. You should know the following:

* How many devices do you expect to have in your network?
* What IPs do those devices have?
* What subnets are defined on the network?
* What types of devices should you expect to see in each subnet?
* What OSs are on the devices?
* What Software are those devices running?
* Are there any unauthorized devices running?
* Is there any unauthorized software on any device?
* What are your critical assets? 

By identifying what's on the network, documenting it, and keeping it up to date will help any organization immensely.

Here are the reasons why this is helpful

## You will be one step ahead of the adversary   

The first step in almost any type of attack is reconnaissance. That is, an adversary needs to first know what's in your network before they can begin to exploit things. If you don't know what is in your network and the enemy does, that is not something you want them to have the upper hand on. This is your inside information that they shouldn't know about.

## Let's you do incident response faster

When doing incident response, a large amount of time will be spent simply on getting to know what is in the network, before being able to mitigate the issue or determine if the threat is even real.

Consider some possible scenarios and then consider how long it would take for you to answer them.

* An IPS unit is triggering alerts saying that someone is trying to conduct a PHP exploit attack against a server on port 4434. How long would it take you to determine if port 4434 is open and if that server is running PHP on that port?

* An antivirus endpoint protection server is indicating that a user's laptop is infected with a virus. How long will it take you to locate that device?

* Your firewall is sending a lot of logs saying that host 192.168.54.75 is trying to go to the internet on port 80 but is being denied by the firewall. How do you determine what host that is in order to determine what the next steps should be?

In all of these cases, the mitigation plan changes depending on what or where the system is that is experiencing the problem. These are all simple problems to fix, but they cannot be fixed until we know more about the network. Sometimes, simply finding more information about the device, takes a lot longer than mitigating the issue.


## It's easier to spot when something isn't right

If your network only has 192.168.x.x subnets within it, then you can set rules that trigger when you see 10.x.x.x or 172.16.x.x subnets. This would be an indicator of something not right. Having that confidence in knowing that certain IPs don't belong on the network, let's you better decisions for defending the network.

## You'll know what needs to be protected

By identifying what assets are on your network, you can then take it a step further and determining which assets are critical to the network. It's probably a good idea to segment the critical assets into a DMZ or behind a firewall to protect those assets even further.

## You'll be able to take action on unauthorized devices

Once you know what devices are authorized to be on the network, you'll be able to safely determine, everything else is unauthorized. You can later set up rules to block or alert when unauthorized traffic is seen in the network.

You can easily spot an unauthorized IP in the network from setting up rules on a firewall.

Spotting something like a users personal laptop or cellphone which may not be allowed on the network may be a little trickier. Authentication logs may be able to check the device to see if it's authorized and if not, create an alert.

## Conclusion

As you see, this beginning step of knowing what's on the network is vital to any organization wishing to start securing the network. Without this step, it makes almost all other steps harder. 

Let me know in the comments below how you store this information. Excel document? IPAM solution? Or is it all in your head?