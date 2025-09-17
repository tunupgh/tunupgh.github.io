---
layout: post
title: Cisco ASA Identity Firewall
date: 2013-06-15 16:01
comments: true
categories:
- cisco
- asa
- scripts
- identity
- firewall
- idfw
- ad
---
The Cisco ASA software 8.4.2 introduced something called Identity Firewall. The IDFW gives a new level of control to ACLs.


## Permit/Deny flows using a user name or user group
You can now permit/deny traffic flows using a user name or user group.

For instance, look at the last two options when making an ACL:

```
ASA-5505(config)# access-list MYACL ext permit ip ?
configure mode commands/options:
  A.B.C.D            Source IP address
  any                Abbreviation for source address and mask of 0.0.0.0
                     0.0.0.0
  host               Use this keyword to configure source host
  interface          Use interface address as source address
  object             Keyword to enter source object name
  object-group       Network object-group for source address
  object-group-user  User object-group for source address
  user               User for source address [<domain_nickname>\]<user_name>
  user-group         User-group for source address
                     [<domain_nickname>\\]<user_group_name>
```
In order for the firewall to map an IP to a username it has to integrate with a domain controller and an AD agent. The AD agent is a program that Cisco has made that is installed on the AD server. It essentially sends the ASA a map of what user has connected with what IP. The ASA then can make decisions on who to block because it knows what each user's IP is. 

The ASA uses the global `user-identity` commands to configure this. 
 
To learn more about installing the AD agent review this link: [http://www.cisco.com/en/US/docs/security/ibf/setup_guide/ad_agent_setup_guide.html](http://www.cisco.com/en/US/docs/security/ibf/setup_guide/ad_agent_setup_guide.html)


## Permit/Deny Domain Names
Also in 8.4.2 there is the ability to block a domain name. This is something we've all been looking forward to for a long time.

#### Configure the ASA to resolve DNS
In order for the firewall to block a domain name it has to be able to resolve domain names. Use your own DNS server if you have it. Otherwise using a public DNS server will look something like this:

```
dns domain-lookup OUTSIDE
DNS server-group DefaultDNS
    name-server 4.2.2.2
```
Make sure you can ping a url like google.com from the firewall to verify your ASA is resolving DNS correctly.

#### Create the object
Suppose we want to block access to Google.com. Create an object with google.com in it.

```
object network OBJ-GOOGLE.COM
 fqdn google.com
```

#### Add the object to an ACL
Now it's just a matter of adding it to the right ACL. Here is what the command looks like:

```
access-list ACL-INSIDE line 1 extended deny ip any object OBJ-GOOGLE.COM
```

This is what it should look like after you do a `show access-list`<br>
```
access-list ACL-INSIDE line 1 extended deny ip any object OBJ-GOOGLE.COM 0x8e6d59a8 
access-list ACL-INSIDE line 1 extended deny ip any fqdn google.com (resolved) 0x827c8780 
access-list ACL-INSIDE line 1 extended deny ip any host 74.125.224.163 (google.com) (hitcnt=73) 0x8e6d59a8 
access-list ACL-INSIDE line 1 extended deny ip any host 74.125.224.165 (google.com) (hitcnt=3) 0x8e6d59a8 
access-list ACL-INSIDE line 1 extended deny ip any host 74.125.224.162 (google.com) (hitcnt=7) 0x8e6d59a8 
access-list ACL-INSIDE line 1 extended deny ip any host 74.125.224.169 (google.com) (hitcnt=23) 0x8e6d59a8 
access-list ACL-INSIDE line 1 extended deny ip any host 74.125.224.174 (google.com) (hitcnt=130) 0x8e6d59a8 
access-list ACL-INSIDE line 1 extended deny ip any host 74.125.224.164 (google.com) (hitcnt=21) 0x8e6d59a8 
access-list ACL-INSIDE line 1 extended deny ip any host 74.125.224.168 (google.com) (hitcnt=14) 0x8e6d59a8 
access-list ACL-INSIDE line 1 extended deny ip any host 74.125.224.161 (google.com) (hitcnt=12) 0x8e6d59a8 
access-list ACL-INSIDE line 1 extended deny ip any host 74.125.224.167 (google.com) (hitcnt=3) 0x8e6d59a8 
access-list ACL-INSIDE line 1 extended deny ip any host 74.125.224.160 (google.com) (hitcnt=20) 0x8e6d59a8 
access-list ACL-INSIDE line 1 extended deny ip any host 74.125.224.166 (google.com) (hitcnt=4) 0x8e6d59a8 
```

Notice the ASA resolved the domain name and added all of the IPs in the ACL. This will be updated based on the TTL of the DNS request it made to resolve the IP.

And this is what the syslog looks like:

`Mar 23 2012 11:58:07: %ASA-4-106023: Deny tcp src INSIDE:172.16.2.21/2414 dst OUTSIDE:74.125.224.160/443(google.com) by access-group "ACL-INSIDE" [0x1e144444,0x1e144444]`

A cool thing about this solution is that it doesn't slow down the firewall at all. It does the DNS look up probably once every few hours for when the TTL expires and then stores the IPs in memory. In other words it does not do a DNS lookup for every packet that comes through the firewall, it does it before hand. 

What I also like about this solution is that this works for both HTTPS and HTTP. The firewall doesn't inspect domain names or URLs and it doesn't care if the packet is encrypted or not. The packet has to have a destination IP and that's what the firewall will check.

** Special Note:** This does not resolve all subdomain of google like www.google.com, mail.google.com, maps.google.com etc. You'll have to create a seperate object for each of these domains to cover all the bases.


 

 


