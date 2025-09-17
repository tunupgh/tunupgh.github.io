---
layout: post
title: Providing a username and password in one line when copying a FTP file to a
  Cisco ASA firewall
categories:
- cisco
- asa
- copy
- flash
- ftp
- scripts
comments: true
---
To copy a file from an FTP server to the flash of a ASA you could do the following:

```
copy ftp flash
```

Which will then prompt you for all the details (username/password/location/file). But there is a better way which will allow you to put all of that information on one line.

To indicate all of the details on one line you can do:

```
copy ftp://ftpuser:Passw0rD@192.168.5.50/asa/asa825-41-k8.bin flash
```

This indicates the username is `ftpuser`, with the password of `Passw0rD` with the location of the ftp server at `192.168.5.50` with the filename `asa825-41-k8.bin`.
