---
layout: post
title: How to SCP transfer a file off of a Checkpoint Firewall
date: 2015-07-26 10:52
comments: true
categories:
- checkpoint
- misc
- scp
---
Sometimes there is a need to move a file off a Checkpoint firewall. Often what I try first is to go into expert mode on the Checkpoint CLI and see if there's a FTP server that I can connect to and transfer the file that way. If that doesn't work then I try SCP.

### Setting up the Checkpoint

From the CLI of Checkpoint, go into expert mode by typing `expert`.

Now change the shell for the user you are logged in as to be the linux command line and not the Checkpoint CLI.

```
[Expert@FW1]# chsh username
Changing shell for username.
New shell [/etc/cli.sh]: /bin/bash
Shell changed.
```

Now make sure the policy on the checkpoint allows TCP port 22 to connect to the checkpoint from the system you want to run the SCP client.



### Using SCP software
Now grab something like WinSCP (unfortunately Filezilla still doesn't do SCP) and launch the program.

Add the IP/username/password you normally use to login to Checkpoint with and choose port 22 if it asks. Once it connects you will be able to browse the files and download the ones you need.


### Finishing up

Don't forget when done to change the shell back to the Checkpoint CLI.

```
[Expert@FW1]# chsh username
Changing shell for username.
New shell [/bin/bash]: /etc/cli.sh
Shell changed.
```
	
