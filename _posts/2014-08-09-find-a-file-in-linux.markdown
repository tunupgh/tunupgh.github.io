---
layout: post
title: Find a file in Linux
date: 2014-08-09 21:25
comments: true
categories:
- linux
- misc
---
Are you trying to understand how to search for a file in Linux? Sometimes the command isn't easy to remember. Try these tips to help you find the file.


## Search by filename

`find / -name 'filename.txt'`

The `/` indicates to start at the root directory. The `-name` tag says look for the filename. The 'filename.txt' is the filename you're looking for.


## Search your current directory and all sub-directories

`find -name homework.txt`


## Search for files ending in .txt in your current directory

`find -name *.txt`


## Find files that are over 5MB in size

`find / -size +5000k`


## Get rid of all the 'permission denied' errors when doing a find

If the command returns an error we can send that output to nowhere by doing the following:

`find / -name 'something.txt' 2>/dev/null`

This will send all errors to the bit bucket and only display successful results.


