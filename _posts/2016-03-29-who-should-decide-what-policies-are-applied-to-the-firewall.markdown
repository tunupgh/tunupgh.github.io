---
layout: post
title: Who should decide what policies are applied to the firewall?
date: 2016-03-29 23:30
comments: true
categories:
- architecture
- misc
---
Firewall rules determine what IPs and ports are allowed through the firewall. We can also consider NAT rules which determine what IPs get translated as they go through the firewall.

So here's a not so simple question. Should the firewall admins/engineers decide what rules should be in the firewall? Or should someone else make that decision?

## Two types of knowledge

Deciding what rules should be in the firewall and making the rule changes in the firewalls are two very different skillsets. We could possibly break those two skillsets into two different titles: firewall engineer, and security architect. 

### Firewall engineer

A firewall admin or engineer has the following knowledge:

* Capabilities, features, and limitations of the firewall
* Commands used on the firewall to complete tasks
* Troubleshooting techniques for resolving issues on the firewall

These skills are highly specialized and focused for that particular firewall. Some vendors will have numerous certifications simply on the topics above. A firewall admin could spend a decade simply trying to master the skills above. They know what is allowed or denied and have the physical ability to change it.

### Security architect

There needs to be another role in order to make the decision of what needs to be permitted through the firewall. This person may have a title such as security architect. Their knowledge would consist of the following:

* Understanding of what is in the network
* Knows what assets are critical to the organization
* Calculates usages such as throughput, bandwidth, and resource utilization
* Understands the organizational needs
* Understands security best practices within a network

This type of person has a much broader vision of the network. They know what should be allowed or denied. They make the decision of what changes should happen on the firewall.

## Two roles for one person

It is probably too much for one person to fill both roles. The more they skill up in one area, the less they may know about another area. This also depends on the size of the organization. It is not too much of a task for one person to fill both roles in a small organization. A medium or large network should consider having different people for these two tasks.

If the firewall engineer was making the decision, they may base their decision solely on what they are seeing in the firewall. For instance, if they see a lot of 192.168.99.0/24 traffic trying to go from the inside of the network out to the internet, they may think that's valid traffic and begin permitting it. They may not have the capability to go research whether that's a valid subnet that should be permitted or not.

Often a firewall engineer will deny as much as they can and wait for someone to request a hole gets opened. If the firewall engineer receives a request, should they simply go ahead and implement the change? Perhaps such requests need to be checked against corporate policy and a change request be submitted for approval first. Should we expect the firewall engineer to create the corporate policy of what is allowed? Their skills are highly specialized for that specific firewall and they may not have the knowledge of what the organization goals are to make such a policy. If a security architect has created a policy, then the firewall engineer can check if the request is approved by the policy before making the change.

The separation of the two roles would further enhance security controls by creating a better auditing trail. If the change requests goes through the architect and then approved by a change committee, then the firewall engineer simply has to implement the requested change. This would greatly limit who has the authority to make changes on the firewall, keeping out people who may not know what they are doing or not having the right permission to do it.

An organization may break the two roles into different levels of engineer. For instance, Jr. engineers could conduct the changes to the firewalls while Sr. engineers have a better understanding of what to expect on the network and can make the decisions of what should be allowed or denied.

Firewall engineers should be updating the security architect with what is being seen on the firewall. For instance, maybe there are a lot of denies for specific ports from devices within the network. The architect would need to know about this in order to make decisions on improving the network design and utilization.

## Conclusion

Perhaps this information will help you identify the roles of your organization better. Please comment below on how this works today in your organization.


