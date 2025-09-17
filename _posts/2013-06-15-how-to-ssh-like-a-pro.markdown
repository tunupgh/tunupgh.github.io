---
layout: post
title: How to SSH like a Pro
date: 2013-06-15 17:06
comments: true
categories:
- ssh
- misc
- workflow
---
A few tips and tricks about sshing. 

Whatever network I'm working in I have found it's common to have a central linux server that I ssh into and then I can jump off of that to any of the devices in the network. The reason for this is that it makes securing the network a lot easier when your VTY ACL consists of one IP even though you have many admins. There's also a trail of who was logged into what when. But by far my favorite reason for having a central server to jump off of is because I can greatly improve my performance because I can use the power of linux to make my life easier.

If you haven't guessed by now, this post is very linux centric keeping in mind accessing networking devices like Cisco routers.

### Aliases in .bashrc
Open up your .bashrc file (use vi or pico or something). Here is an example of what mine looks like:

``` bash
alias s='ssh'
alias sc='ssh -l cisco'
```

I like to think smarter whenever I can. By making `s` the alias for ssh it cuts down the amount of characters typed by 66%! So now whenver I need to ssh into a device I simply use `s` like so:<br>
`s 192.168.1.1`

Also often I am finding myself using the same username like `cisco` for many things. If I know the username is `cisco` before trying to ssh to the device I now have a shortcut for that. To ssh into a system using that username I simply do:<br>
`sc 192.168.1.1`

### Editing your .ssh/config file
By default your ssh username will be whatever you logged into linux with. So how do you change the default ssh username? That's simple. It's in the `.ssh/config` file.

Edit the file:<br>
`vi .ssh/config`<br>
Add the lines:<br>
`Host *`<br>
` User myname.acs`<br>
Now change permissions of the file:<br>
`chmod 600 .ssh/config`

Now simply try to ssh into any device and you'll see the username has changed to `myname.acs` by default.

#### Changing the username for a specific device or subnet
Let's say you have a bunch of devices in the 192.168.5.x network that all authenticate to the same username 'bob.acs'. It's possible to configure ssh to lookup the place you're trying to go and tack on a username before trying to ssh. Here's how.

Go back into editing the file:<br>
`vi .ssh/config`<br>
Add the following lines at the top of the file:<br>
`Host 192.168.5.*`<br>
` User newusername`<br>

Now you've just changed the default username for just devices starting with 192.168.5.x. This can also work for hostnames too.

### Use keys to login instead of a password	

If you're trying to ssh from one linux machine to another it's possible to use keys for authentication instead of a password. Follow this guide to [ssh without password](/ssh-without-password).

### Port-forwarding and Tunneling

You can also do [port-fowarding and tunneling with ssh](/how-to-create-ssh-tunnels/). 



