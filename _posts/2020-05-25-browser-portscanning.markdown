---
layout: post
title: Everything You Need to Know About Websites Port Scanning You
date: 2020-05-25 16:47
comments: true
categories:
---


Some websites can and do port scan you when you visit their site. This is sneaky and creepy behavior and I want to tell you about it.

### What it looks like

First of all this is not your run of the mill port scanning. This isn’t someone firing off a bunch of packets at you on different ports looking to see what you have open. No this is all done in the browser through javascript, where a website is instructing your computer to port scan itself, then report the results to the website.

Today I loaded up Microsoft Edge browser (version 44), enabled developer tools, looked at the network traffic and visited eBay.com. This is what I saw.

<img src="/images/portscan1.jpg" alt="eBay Port Scan">



As you can see the website has triggered a series of GET requests to 127.0.0.1 on 14 different ports. 127.0.0.1 is the loopback address of every computer in the world. If try to connect to that IP you will always be connecting the computer you are on. So when you visit the website, it instructs your computer, to check if these 14 ports are open, on your own computer.

These ports are:

<table class="table"><tbody><tr><th>Program</th>
<th>Name</th>
<th>Port</th>
</tr><tr><td><strong>Unknown</strong></td>
<td>REF</td>
<td>63333</td>
</tr><tr><td><strong>VNC</strong></td>
<td>VNC</td>
<td>5900</td>
</tr><tr><td><strong>VNC</strong></td>
<td>VNC</td>
<td>5901</td>
</tr><tr><td><strong>VNC</strong></td>
<td>VNC</td>
<td>5902</td>
</tr><tr><td><strong>VNC</strong></td>
<td>VNC</td>
<td>5903</td>
</tr><tr><td><strong>Remote Desktop Protocol</strong></td>
<td>RDP</td>
<td>3389</td>
</tr><tr><td><strong>Aeroadmin</strong></td>
<td>ARO</td>
<td>5950</td>
</tr><tr><td><strong>Ammyy Admin</strong></td>
<td>AMY</td>
<td>5931</td>
</tr><tr><td><strong>TeamViewer</strong></td>
<td>TV0</td>
<td>5939</td>
</tr><tr><td><strong>TeamViewer</strong></td>
<td>TV1</td>
<td>6039</td>
</tr><tr><td><strong>TeamViewer</strong></td>
<td>TV2</td>
<td>5944</td>
</tr><tr><td><strong>TeamViewer</strong></td>
<td>TV2</td>
<td>6040</td>
</tr><tr><td><strong>Anyplace Control</strong></td>
<td>APC</td>
<td>5279</td>
</tr><tr><td><strong>AnyDesk</strong></td>
<td>ANY</td>
<td>7070</td>
</tr></tbody></table>


By the way, thank you to Charlie Belmer for his blog post Why Is This Website Port Scanning Me which introduced me to this problem, and I only caught that post through this Hacker News posting.

Also if you want to test this on your own, be aware that eBay only does this on the first visit to the website.

### How to Block This

Ok first, strait up, this is creepy and I think it’s invasive. I do not want to allow websites to do this to my computer. It’s bypassing all the network security I have put in place. It bypasses my firewall, my AV, and my Pi Hole. Because the website instructed my computer to conduct these scans, and it’s not coming from the internet.

### So how do you stop it?

There are two browser extension that seem to do pretty good at stopping this:

Privacy Badger and uBlock Origin.

But some browser block this behavior by default too. Brave and Opera seem to block this by default.

Users have reported to me that they see this scanning behavior is permitted in Chrome, Firefox, and Edge when they have no extensions enabled.

Also it’s worth noting that the tor browser blocks this specifically.

### eBay is not the only ones doing this

After raising these concerns on Twitter I started getting information from people far and wide. I quickly learned that eBay is using a solution called Threat Metrix to conduct this. And here’s a client list.

<img src="/images/portscan2.jpg" alt="Port Scan Info">

Some big names on this list. GoPro, WePay, Netflix, Visa, Yandex, TripAdvisor, and a bunch of dating sites and finance sites. I visited 5 of these sites and watched the network traffic and didn’t see the same scanning behavior. But I guess ThreatMetrix doesn’t just offer scanning like this, but has other features too. So who knows what they are doing here. Perhaps extra identity proofing happens only when you login or when you go to purchase something. So potentially all of these have the technology to do this to their customers.

But wait. That’s just one vendor. One solution. Surely they have competitors right? Here’s a report from Gartner regarding Identity Proofing.

In this there are a list of Recommended Vendors.

> Gartner named Acuant, BioCatch, Buguroo, Emailage, Experian, EZMCOM, Fraud.net, IBM Trusteer, ID Analytics, IdentityMind, IDology, iovation, Jumio, Kount, LexisNexis Risk Solutions, Mitek, Nuance, NuData Security (A Mastercard Company), Pindrop, SecuredTorch, ThreatMetrix (A LexisNexis Risk Solutions COmpany), TransUnion, TRUSTID, and Whitepages Pro as Representative Vendors in the market.

ThreatMetrix is in there. But there are so many more. Not all of these do in browser port scanning. But some certainly do.

But whoa. This suddenly became a huge market. This means that there are likely a lot of websites doing this.

But wait, this isn’t new either. Facebook was scanning their visitors back in 2016.

### So why are they port scanning us?

I think the easiest way to explain this is to look at ThreatMetrix’s feature list. Here’s a snippet from their website.

* Context-based information detects bot attacks to perform behavioral analysis of users during periods of normal operation and compares such data to that gathered during an attack, enabling the ability to differentiate between a human and a bot the moment they login/transact.
* Deep connection analysis technologies detect the use of technologies such as hidden proxies and VPNs and allow the bank to see the true IP address, geolocation and other attributes of each event, backed by global identity data over time.
* Malware protection helps businesses mitigate the risk by being protected from Man-In-The-Browser (MITB), Remote Access Trojan (RAT), high velocity/frequency bot attacks to low-and- slow attacks mimicking legitimate customer behavior, ransomware, key logging attempts, etc.
* Trust Tags enable businesses to define, categorize, tag and differentiate between good and bad users, devices, locations or personas.
* SmartID identifies returning users that wipe cookies, use private browsing, and change other parameters to bypass traditional device fingerprinting tools. This improves returning user detection and reduces false positives.
* TrueIP accurately detects the use of location and identity cloaking services, such as hidden proxies and VPNs, allowing your business to see the true IP address, geolocation and other attributes of each transaction.
* Whoa nelly those are some impressive features! Ok let’s break this down. Obviously the port scan is only a part of the solution. But combine that with other data collected on the user, then cross reference that with other data and these companies can develop a rich understanding of the user. The more the company knows about the user the better they can serve the user. Or track the user. Or deliver custom ads for the user. In some ways the user benefits but in many ways the user is losing a lot of privacy.

But fraud reduction seems to be the big reason why websites are doing this. If you noticed, all the ports scanned are related to remote access to a computer. There are a lot of scams where an attacker will take control of the victims computer, then try to buy something on Amazon or send money from the victim’s bank to the attacker’s bank. Websites have seen a lot of chargebacks, saying they didn’t buy something that was purchased on their account. When the website tracks this purchase it shows it was from the same browser the user usually uses, the same IP everything. But the computer was remotely controlled. So by checking if any ports are open which would indicate the computer is being remotely controlled, this can help websites combat unauthorized purchases.

Besides helping victims stay safe from this kind of attack, websites have also seen fraudsters purchase server space in the cloud then use that server to try different usernames and passwords with. Because the fraudster can just cancel their cloud server and get a new one and the website can’t track them. So this is another reason why websites want to know if their visitors are being remotely controlled.

Here’s one article I found which says Lloyd’s Bank has a technology to sniff out scammers. It says it has a “new high-tech defence system” to do this. I have a strong sense that this is the use of ThreatMetrix or a competitor. And what’s funny is this is on TalkTalk’s website, which has historically been hit hard by scammers who call up TalkTalk customers and con them out of money.

### Is this illegal?

Ah the burning debate. Recently NordVPN Tweeted this:

<img src="/images/portscan3.jpg" width=300px>


And a lot of the InfoSec community piled on NordVPN for saying such ridiculous things. I think we didn’t understand that this kind of javascript browser local port scanning is even a thing. So it made no sense to us that a browser extension can block this. But also with companies like Shodan.io who portscan the entire internet legally, everyone was saying port scanning is not illegal. I mean if you open port 80 on your server and someone checks to see if it’s open, that’s perfectly legal. This Tweet resulted in a lot of people calling NordVPN bad names. So did NordVPN say anything wrong here?

To begin with this kind of port scanning is totally different than what Shodan is doing. Shodan is going to every IP on the public internet and checking what ports are open. But what eBay is doing is port scanning my PC in my private network in my home. Which is behind a firewall, and it’s a restricted network! It’s the difference between someone driving by my home, and looking to see if I left my front door open vs someone coming INTO my home, and checking if my bedroom door is open. I can’t control what goes on outside my house and I get it’s public space. But I do not authorize anyone to port scan computers inside my house! So at least to me I do not consent to this, and this is not ok with me.

Let’s take a look at this Tweet from 2018.

> Halifax Bank are illegally carrying out a local port scan, checking for open VNC/RDP ports. Without consent, this breaches CMA. They disagree, 3 solicitors agree.
> Is anyone brave enough to take this on?
> cc @BBCTech @ruskin147 @jleyden @Chrisitv @kevinpashby @dangoodin001 pic.twitter.com/BSAwXWzTKb
> — Paul Moore 🇬🇧 (@Paul_Reviews) August 3, 2018

Back in 2018 Paul saw that halifax.co.uk was port scanning his PC when he visited it. Paul believes this is a direct violation of the Computer Misuse Act in the UK. And right in section 1 part 1 a it says:

> A person is guilty of an offense if—he causes a computer to perform any function with intent to secure access to any program or data held in any computer

That is quite a broad statement! It’s saying anyone who accesses any secure data on a computer is guilty of the CMA. This is a double edge sword. On one hand yes this does mean that accessing information about what ports I have on my personal computer is illegal, but on the other hand, you can use this to convict anyone. This is just way too broad to be used as a basis for anything!

So I think a judge will then have to look for intent. Does ThreatMetrix have intent on doing something malicious or harmful with the secure data they acquired through this scan? At the surface, no. They are doing this to improve customer experience and combat fraud. But what if they are negligent with their own security and get breached and now a ton of personal data about me is leaked. I did not opt in to give this data to ThreatMetrix. So if they are negligent with my data then yes absolutely they can be fined and face punishments by a court.

Halifax bank, which is owned by Lloyds Banking Group, which is a listed as client of ThreatMetrix, was never brought to court for this and continues to use this software.

This is one of those situations that this has gone right up to the edge of the line on whether it’s legal or not. It’s a gray area. And in this case it seems to be used for good (although I don’t approve), but what happens when websites adopt this technology with malicious intent? Then this port scanning behavior will become criminal activity.

A journalist at Forbes reached out to eBay for a comment and they said:

> “Our customers’ privacy and data remain a top priority. We are committed to creating an experience on our sites and services that is safe, secure, and trustworthy.”

And that makes sense. They are doing this to improve security of eBay’s assets, but it’s at the expense of giving personal information of users to ThreatMetrix. So is the gain better than the loss?

This also doesn’t seem to be adhering to GDPR data privacy regulations. So this probably in direct violation of that at the minimum.

Lastly you should take a look at this. Here is a sample of data that ThreatMetrix collects on each visitor to the site through their js tracking tools.

This reminds me of Panopticlick. If you run the test and click show additional data, you’ll see how much info a website can collect on you. I ran it, and my browser data was completely unique compared to the hundreds of thousands of others who ran it. Meaning all this data my browser gave the websites means they can track me even if refuse to allow cookies and other tracking methods.

If I had more time I would try playing with the javascript to see if I could get it to port scan other devices in my local network. Because I think that would be going over the line on legalities. I’m not a lawyer so I don’t know. But what I’ve seen from court history is that it’s not the law that matters, it’s whoever has the better lawyer that wins.

So maybe we owe NordVPN an apology for piling on them like that. They have removed their Tweet due to the enormous backlash they got. They weren’t saying that a VPN can fix it, instead they were linking to a blog post showing what browser extensions can help keep you more secure, which is a fine blog post. Those extensions do block this port scanning. I don’t think they deserved the backlash they got from that Tweet. This is a real problem and quite possibly illegal if not now, then it will be soon when someone abuses this.

### Wait, technically, how is this possible?

Ya good question. Well for this I defer to Dan Nemec who breaks down the javascript involved.

https://blog.nem.ec/2020/05/24/ebay-port-scanning/

There is also an interesting article by Bleeping Computer on this.

Lastly, a lively conversation took place on Hacker News regarding this too.

