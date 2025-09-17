---
layout: post
title: Cisco MARS how to get logs off it
date: 2015-10-18 16:31
comments: true
categories:
- cisco
- mars
- scripts
---
Below is the procedure below explains how to get logs off Mars Box from [expert mode](/cisco-mars-siem-expert-password) using FTP.

If you have an scp server you can use the `pnlog scpto` command. But if all you have is an FTP server, then this is more complicated. First we do the `pnlog scpto` command, but we do that only so the system packages up the logs pretty for us.

```
[pnadmin]$ pnlog scpto test
scp /tmp/error-logs.tar.gz tmp/error-logs.tar.gz
cp: cannot create regular file `tmp/error-logs.tar.gz':
No such file or directory Fail to copy error-logs.tar.gz, skip copying janus-log.tar.gz.
```

The argument 'test' is just a name. Syntax pnlog scpto <anything>. But even when scp fails, mars copies the file to 'tmp' directory. It saves some steps of logging into expert mode and copying files over to tmp directory.

```
[pnadmin]$ expert Password:
[expert@TRAINING-MARS]# su
[root@TRAINING-MARS bin]# cd /tmp
[root@TRAINING-MARS tmp]# ls
[root@TRAINING-MARS tmp]# mv error-logs.tar.gz 60011122error-logs.tar.gz
[root@TRAINING-MARS tmp]# mv janus-logs.tar.gz 60011122janus-logs.tar.gz
```
The `error-logs.tar.gz` and `janus-logs.tar.gz` files are where the logs are stored.

Optional: Rename the files, making it easy to find on a ftp server, when they are copied over. Only way to rename files is to move them by `mv` command in unix.

```
[root@TRAINING-MARS tmp]# ftp <ftp IP address>
Name (ftp IP): <username>
331 Password required for < > Password: <password>
ftp>
ftp> cd /incoming
250 CWD command successful.
ftp> bin
200
ftp> put 60011122error-logs.tar.gz
local: 60011122error-logs.tar.gz remote: 60011122error-logs.tar.gz 227
Entering Passive Mode (64,102,255,116,160,128) 150
Opening BINARY mode data connection for 60011122error-logs.tar.gz 226
Transfer complete. 1987611 bytes sent in 0.96 seconds (2e+03 Kbytes/s)
ftp> put 60011122janus-logs.tar.gz
local: 60011122janus-logs.tar.gz remote: 60011122janus-logs.tar.gz 227
Entering Passive Mode (64,102,255,116,160,129) 150
Opening BINARY mode data connection for 60011122janus-logs.tar.gz 226
Transfer complete. 4301304
ftp> bye
421 Idle Timeout (120 seconds): Closing control connection
```
