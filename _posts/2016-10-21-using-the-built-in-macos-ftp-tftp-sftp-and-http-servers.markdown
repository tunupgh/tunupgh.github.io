---
layout: post
title: Using The Built-In macOS FTP, TFTP, SFTP, and HTTP Servers
date: 2016-10-21 22:02
comments: true
categories:
- macos
- mac
- osx
- ftp
- tftp
- sftp
- http
- misc
---
The macOS and OSX operating systems come with built in FTP, TFTP, SFTP, and HTTP servers. Here is how to enable them and use them.

## FTP Server
To start an FTP server use this terminal command:

```
sudo -s launchctl load -w /System/Library/LaunchDaemons/ftp.plist
```

Users will need to authenticate to the macOS system using standard logins that are already on the macOS. When someone logs in, they will arrive in `/Users/<username>/`.

To stop the FTP server, run the following:

```
sudo launchctl unload -F /System/Library/LaunchDaemons/ftp.plist
```



## TFTP Server

```
sudo launchctl load -F /System/Library/LaunchDaemons/tftp.plist
sudo launchctl start com.apple.tftpd
```

The directory that serves files is `/private/tftpboot`.

To stop the TFTP server, either reboot or use this command:

```
sudo launchctl unload -F /System/Library/LaunchDaemons/tftp.plist
```


## SFTP Server

This one is different. Go to system preferences then `Sharing`. Check the box to enabe `Remote Login`. Once this is done, other people will be able to ssh and sftp to your macOS system.


## HTTP Server

Apache comes bundled with macOS. To start it, run this command.

```
sudo apachectl start
```

By default the files will be served from `/Library/WebServer/Documents`. 

To disable the server, either reboot or do this command:

```
sudo apachectl stop
```

## Checking Status

To check to see what's running, run the command:

```
netstat -an | grep LISTEN
```

This will show you which ports are open on your macOS. For example if you have Apache running, you will see the following:

```
tcp46      0      0  *.80                   *.*                    LISTEN
```
 
* HTTP = TCP 80
* FTP = TCP 21
* SSH = TCP 22
* SFTP = TCP 22
* TFTP = UDP 69

Note that TFTP is on UDP and won't say listen. Just run `netstat -an | grep 69` to find if it's running.



