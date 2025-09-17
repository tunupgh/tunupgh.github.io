---
layout: post
title: Six Ways to Protect Your Corporate Network Against Phishing Scams
date: 2016-04-16 16:04
comments: true
categories:
- opsec
- phishing
- infosec
---
It has been said that a company is one email away from a huge breach disaster. In today's landscape, people can serve as the greatest vulnerability to the network. How can we improve this?

### What kind of damage is possible?

In the last few years there have been a rash of phishing scams where the scammer claims to be the CEO of the company and asks for a large amount of money be wired to a bank account. This type of phishing is sometimes known as whaling. The scammers will go to great lengths to watch the emails the CEO sends and gets familiar with how they talk and who has the ability to send such large amounts of money.

Even if a company has cyber insurance, this isn't covered by insurance. <a href="http://krebsonsecurity.com/2016/01/firm-sues-cyber-insurer-over-480k-loss/">Ameriforge lost $480,000 in this scam</a> but was denied an insurance claim. <a href="http://www.networkworld.com/article/2984989/security/cyber-insurance-rejects-claim-after-bitpay-lost-1-8-million-in-phishing-attack.html">BitPay lost $1.8Million to this scam</a> and also was denied an insurance claim.

W-2 phishing is another major concern. This year, <a href="http://krebsonsecurity.com/2016/03/seagate-phish-exposes-all-employee-w-2s/">Seagate</a>, <a href="http://money.cnn.com/2016/02/29/technology/snapchat-phishing-scam/index.html">Snapchat</a> and <a href="http://www.ktva.com/more-than-2500-gci-employees-w-2-information-stolen-in-phishing-scam-524/">GCI</a> all reported that hackers have stolen thousands of W-2 from their employees. Learn more about how [who is behind the W2 scams](/actors-behind-the-w2-tax-phishing-scams/).

Using a combination of <a href="https://www.trustedsec.com/social-engineer-toolkit/">SET</a> and social engineering, a hacker could call a user within a company, get them to go to a malicious URL which infects their machine, and then take over their machine. Now that the hacker is inside the company, it could possibly have full access to the entire network, depending on who's machine they infected and how the network is deployed. This could have devastating effects. Anything from, destruction of data, to leaking all the company secrets, to stealing any bitcoin/pre-paid credit cards/money, to stealing all the employee's HR records.

### How can we protect against this?

There are a number of ways to protect against such attacks. Here are 6 suggestions that could save the company millions of dollars.

#### 1. Segmentation of the Network

This is becoming one of the most important strategies for securing a network. Think of your network like a massive ship. Modern ships are designed to with stand a few punctures to the hull without sinking. It can withstand this damage because of compartmentalizing areas of the hull so that if water gets in one area, it doesn't spread the to the entire hull. Your network should have the same resilience. If one system is hacked, it should not have full uncontrolled access to the entire network.

There should be a firewall barrier that separates all of the following segments:

* The public internet
* LAN Users (possibly segment by department)
* VPN Users
* Wireless Users
* Internet facing servers
* Internal facing servers
* Highly critical servers
* Partner/Vendor/B2B networks
* Dev/Lab servers
* Customer networks
* Remote offices

The goal here is to only permit the traffic which has a business need to traverse each segment. Of course, if you don't know what servers you have or what applications are in your network, that would be something to figure out also. You don't want your users having the ability to exploit servers on your network that were misconfigured or unpatched. By placing a firewall in between these segments, allows you to only permit the port that user needs to access that server, like http.

#### 2. Antivirus on User's Computers

Users are often clicking links they shouldn't or going to places on the internet that are dangerous to their computer. If your user clicks on a bad link, this will likely attempt to execute something it shouldn't on the user's machine. These known exploits are often detected and blocked by Antivirus or Endpoint protection software. Having these technologies present on the user's machine could save the user and company from a major disaster.

#### 3. Patching

The majority of the attacks and exploits out there are old and well known that don't work on patched computers. It should be standard practice today that all systems in the network have a patch cycle and any unused software is uninstalled. Sometimes, you may patch the OS but forget to patch all the applications that's also installed. To save time and headaches, uninstall any applications that aren't used. The more applications running on a computer, the more likely it is to become vulnerable to attacks.

#### 4. Security Awareness Training

Because the user is one of the weakest links in the network security architecture, it's crucial to conduct security awareness training. If you think security awareness training is not very effective, check out these [unique ways of improving it](/effective-security-awareness-training/).

Along these lines, it's good to set up an internal way to report potential scams or security problems your employee's have seen. If they see something and don't know who to tell, there is a problem with your security program.

#### 5. Domain management

A lot of these phishing scams use look-alike domain. If you worked at example.com could you recognize the problem in this email header?

> From: CEO@examp1e.com

The "L" was switched with a "ONE". This can be incredibly difficult to catch and so look-alike domains have been very effective at tricking people for decades. Here's some tips for combating this.

* Make sure your DKIM and SPF DNS records are set correctly. Without these settings configured correctly, anyone can spoof mail from your company. With these records present, when someone gets an email, if they are following RFC practices, they will check the settings to see if where they got the mail from matches where it's supposed to come from, thus catching a spoofed email.

* Look for advances in the <a href="https://en.wikipedia.org/wiki/Levenshtein_distance">Levenshtein algorithm</a>. This is a algorithm that compares two strings to see how similar they are. In the case of `example.com` vs `examp1e.com`, this would have a Levenshtein distance of 1, since it's one character different. Products like MS Exchange are looking into ways to use this algorithm to block incoming mail that has a very low distance, which could effectively block look-alike domains from entering your network and confusing your employees.

#### 6. Monitor Password Breach Dumps

Consider this scenario. Bill works for your company. He uses his company email to sign up for some ecard web service. That ecard web service then gets breached, and Bill's email address, username, and password are stolen from the ecard website. Bill is now at a higher risk for being spear phished. Hackers could now know the following details about Bill:

* What his email address is
* What he likes to use as a password
* Where he works
* What his interests are (he likes ecards!)

If the hackers got more information they may even be able to see who he sent ecards to or what his secret fallback question is. From here the hackers could then try to go to your company's web mail and see if that username and password works there. With some additional research, hackers could get a lot of information about Bill.

Bill is now at high risk of being spear phished. With all this information, a hacker could call him, claiming they know him or try to blackmail him into doing something. By monitoring these password dumps, you could then do additional awareness training with Bill, force him to reset his credentials, and possibly add him to a special group.

Check out the websites <a href="https://haveibeenpwned.com/">HaveIBeenPwned.com</a> or [ThreatMiner.org](https://threatminer.org) to get started looking for leaked emails.

### Final thoughts

Do you have other more effective ways of stopping phishing attacks? If so, let us know in the comments. One thing I wish I could get better visibility on is the ability to know how effective each of these strategies are. There isn't a way I know of to review metrics for this.


