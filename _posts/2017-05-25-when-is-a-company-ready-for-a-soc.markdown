---
layout: post
title: When is a company ready for a SOC?
date: 2017-05-25 08:29
comments: true
categories:
- infosec
- soc
---
More and more companies are deciding to outsource their information security to another company. A Managed Security Service Provider (MSSP) is exactly that, a company that will handle the security of another company. Some MSSPs provide Security Operation Center as a service. This is the fastest and cheapest way to get a SOC. To build a SOC from scratch requires a lot of time, tools, and people. But when is a company ready to get a SOC?

### 3 Main Types of Threats

To understand when a SOC is needed, we must understand what types of threats there are.

**Generic** threats are those of the "spray and pray" type. Imagine a vulnerability scanner, scanning the entire Internet. Imagine global worms like the WCRY (WannaCry) ransomware. These adversaries are just looking for any vulnerability on any systems in any part of the world.

**Targeted** threats are attacks against a specific target or software. This may be someone who's goal is to damage a specific company and they will scan/social engineer/attack any open portal the company has exposed to the public. Another type of targeted attack is against specific software, like specifically looking for WordPress sites and attempting to infect those only.

**Persistent** threats or otherwise known as Advanced Persistent Threat (APT) is when a highly skilled person or group is using numerous resources to infiltrate a company's network. Picture a tactical team of hackers with a specific mission who has access to unknown vulnerabilities (0 days) or a large pool of manpower, or a large pool of money dedicated all to a single attack. Not many groups have the resources to conduct an APT. Usually when a hacker group is conducting an APT it is for specific reasons such as political.

### Defending Against These Attacks

Now that we have classified the 3 main types of attacks we can look at ways to defend against each of them.

To defend against a **Generic** attack, an organization should deploy best practices for securing a network. This includes not exposing unneeded ports to the Internet, patching systems, and hardening all devices.

To defend against a **Targeted** attack, an organization should architect the network with security first. This includes segmenting VLANs, denying remote logins to administrator and root, deploying firewalls, IPS units, load balancers, antivirus solutions, and setup loggers and data collectors. Create rules on loggers and data collectors that notify sys admins when a security incident has occurred in the network. In this approach we now consider the network to be fully secured and every effort put in place to stay secured.

To defend against a **Persistent** attack, an organization should be monitoring their logs, snmp traps, netflow, and sometimes full packet captures, 24/7/365 with humans and systems doing the monitoring. That is, the use of a SIEM along with technicians watching the SIEM 24 hours a day. Once an incident is detected, action should then be taken to mitigate the attack. This is called a **SOC**. The theory here is that an APT is usually done by humans who are extremely skilled at exploiting systems, so the defending team needs to also have humans who are skilled at detecting when a system is being exploited. No longer will passive defenses work such as firewalls and IPS units during an APT. Active defenses such as responding to the first sign of an attack are how to defend against this type of attack. Besides having a SOC to detect against these threats, an organization may have additional DFIR teams (Digital Forensics & Incident Response) which can determine the who/what/why/how of an attack.

### Conclusion

Before an organization decides to get a SOC, they should decide what types of attacks they will be facing and why. Everyone is victim to generic attacks, and most companies face a targeted attack on a regular basis. APTs are more rare and doesn't target every organization but takes the most amount of resources to defend against.

Information Security should already be a major focus on a company before they get a SOC. The network should be architected with security first and systems should already be hardened to their best effort. A SOC will spot many security issues and if the admins aren't already capable of securing systems they may not put a lot of focus on recommendations or findings from the SOC. If leadership has already been encouraging admins to focus on security then when SOC alerts come in, the admins should be ready to resolve the issues. It would be a waste of money to create a SOC only to ignore the alerts they report on.

SOCs can also interact with such teams like Malware Investigators, Digital Forensics team, Incident Responders and Threat Intelligence teams. These are obviously even more specialized teams that a company can to make responding to incidents faster, more efficient, and more accurate. These teams can sniff out where the malware came from, who did the attack, what data was leaked from the system and so on. If the organization doesn't care to have this information, and simply rather remove the malware and move on, then these teams aren't needed.