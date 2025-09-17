---
layout: post
title: What is VPN?
date: 2014-11-08 19:53
comments: true
categories:
- misc
- vpn
---
A Virtual Private Network is a technology used by computers to securely connect two networks together across an insecure network such as the internet. It is often used to connect two offices together or for a remote worker to connect to a private office network. A VPN tunnel relies on encryption and integrity to pass data through it. When the traffic is strongly encrypted, it is unreadable by anyone who intercepts it. Data integrity simply ensures that the data that went into the tunnel is the same as the data that comes out of the tunnel.

![](/images/vpn1.png)

The internet is considered a public and insecure network. That is mainly because it is not owned by you. When your data travels over the internet it goes through a dozen or more systems. The owners of those systems and possibly the government can easily see all of the data going through it if it uses insecure protocols. Even if you connected to the WiFi of a coffee shop, that network may be listening to every packet leaving your computer and reading it. A VPN tunnel is a solution to encrypt your data so it can cross over an untrusted network without the fear of your data being read.

In order for a VPN to be created there must be two systems that set up the tunnel. This may be two routers or two firewalls or even two laptops or computers. The two devices are called peers and must agree on what traffic will pass over the tunnel, which encryption to use, and how to exchange their keys. Sometimes only specific traffic should go over the VPN and the rest of the traffic can go over the regular network. This would be called a split VPN. When all traffic is encrypted from one end then that is considered a full VPN.

### Using a Private VPN for Browsing the Web Anonymously

Some people wish to use a VPN to encrypt the traffic leaving their computer or home network and mask their location. When a full VPN is created from one computer to another system all the traffic will appear to come from the remote end of the VPN when traveling over the internet. An example would be if someone from the US created a full VPN tunnel to Canada. When the person in the US surfs the internet, it will have a Canadian IP and websites will believe it is someone from Canada who is accessing their website. This allows a user to become more anonymous online. If you change the country of origin of your internet surfing, you could enable or disable yourself from accessing specific content. This is a way to get past any country rules that may block content.

![](/images/vpn2.png)

There are many sites out there that offer private VPN services. This allows a user to sign up for their service and VPN into that network. The user would have their data encrypted to the peer network and traffic that goes to the internet will appear to come from that peer network. This would prevent the coffee shop owner from seeing where you are going and provide greater anonymity of your web surfing. Here are a few VPN providers that offer such services. Each of these sites are pay services and range from $5-$12/mo.


##### [Private Internet Access](https://www.privateinternetaccess.com/)
This website allows the user to choose which exit node to use out of their 1000+ nodes. They have won many awards and are highly recommended by their users.

##### [TorGuard](http://torguard.net/)
TorGuard has different services based on what your needs are. They are very fast and have a great uptime record. 

##### [IP Vanish](https://www.ipvanish.com/)
Another private VPN service provider that has thousands of IPs in numerous countries that is both fast and reliable.


### Site to Site VPN
Companies that have more than one location often need to share resources between the offices. To connect the networks together in both offices so a remote office can access resources in the main office a site to site VPN is created. This would likely only encrypt traffic between the two sites but if a remote office needs to access the internet it uses its local internet connection. This website (TunnelsUp.com) has numerous articles about creating this kind of VPN and troubleshooting them when there is a problem.

When a site to site VPN is created the termination points are often routers or firewalls. A firewall has VPN capabilities usually by design. A router may not have VPN capabilities by default. The two sites would have to have similar configurations in order to agree to create a tunnel between each other.

### Remote Access VPN
When a remote worker is away from the office and needs to access resources in the office network a remote access VPN is created. This is usually a VPN that is initiated from a the user's computer that connects to a device like a router or firewall in the main office. This allows the user to securely access files within the companies network. Technologies used to complete this may be SSL VPN or AnyConnect VPN.

