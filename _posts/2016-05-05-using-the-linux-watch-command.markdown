---
layout: post
title: Using the Linux Watch Command
date: 2016-05-05 08:50
comments: true
categories:
- misc
- linux
---
A somewhat lesser known linux command is `watch`. It will display the output of a regular command and update the screen if that output changes. Here are some helpful use cases.

## Watch for directory contents changing

Try the command:

`watch -d ls -la`

You'll see the output of `ls -la` and every 2 seconds the screen will refresh if there are any changes. If files grow in size, get removed, or added, you'll see it highlighted on the screen when it happens.

The `-d` switch will highlight changes for you to make it easier to spot.

## Watch disk space grow or shrink

Try this command:

`watch -d df -h`

You'll see an output of the free disk space in human readable format and if it changes it will update live every 2 seconds. 

## More info

See `man watch`.




