---
layout: post
title: ssh Remote Host Identification Has Changed
date: 2015-02-28 20:41
comments: true
categories:
- misc
- ssh
- linux
---
Sometimes when you are trying to do ssh from a linux machine you may see this message:

```
user@server:~$ ssh -l user 192.168.1.1
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the RSA key sent by the remote host is
7f:9a:dc:fd:c8:a2:0b:be:c7:b1:39:5c:fe:a6:a3:2d.
Please contact your system administrator.
Add correct host key in /Users/user/.ssh/known_hosts to get rid of this message.
Offending RSA key in /Users/user/.ssh/known_hosts:5
RSA host key for 192.168.1.1 has changed and you have requested strict checking.
Host key verification failed.
```

## Host key verification failed

When you see the message above it indicates that the ssh key found in your known_hosts file has a different key than the system you are trying to ssh into. This usually happens if the destination changes but the IP remains the same. For instance if this IP is a router on the network that you've ssh'd into before and the router gets replaced with a new one, the new router will have a different ssh key. The message indicates exactly which file and even what line the ssh key is located in. 


If you trust the destination you are trying to get to is the right one the best way to fix this is to go delete the offending ssh key.

Use your favorite text editor such as vi or nano and edit the file. Here we'll use vi:<br>
`vi /Users/user/.ssh/known_hosts`

The location of this file will be in the error message you received earlier.

Then you can either search for the the IP of the system you're trying to ssh into like:<br>
`/192.168.1.1`

Or go directly to the line that it indicated in the error message:<br>
`:5`

The line should look something like this:

```
192.168.1.1 ssh-rsa AAAAB3NzaC1yc2EAwgYKwAAqBe9mgpPGQDJRBEHv2mjuZn1e1v9GPKoNL+09zX0khWtayio5Oa6Y0BcTBeHBmCWMilqsrZIAvetB5nmcQeWkjX5fcgZsztl1ro3oenxQd0PQIO0WA6A79zsL36A25iwgVkFq2IFscYWoJ7b005ja6GJAAmjAANff8XFdgZR8+DP23EMTM11pIsK2w==
```

Delete the line save and quit.<br>
`dd`<br>
`wq`

Now try your ssh session again and you should no longer be getting the error. 

If there is a problem with a the `known_hosts` file getting screwed up it is safe to delete the `known_hosts` file entirely. It will also delete any of your saved ssh keys but you will simply be prompted to accept them next time you ssh to something.








