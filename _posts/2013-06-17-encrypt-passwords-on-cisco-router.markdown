---
layout: post
title: Encrypt Passwords on Cisco Router
date: 2013-06-17 22:26
comments: true
categories:
- cisco
- troubleshooting
- passwords
- router
- switch
- ios
---
By default, when adding a username and password to a Cisco router or switch, the password will show up as clear text. So the question then becomes:
### How do you encrypt the password on a Cisco Router?
You should avoid creating a username like this:<br>
`username TESTUSER password TESTPASS`

This will result in a clear text password in the configuration. To encrypt all of the passwords after that has been done you can do the following command:

`service password-encryption`

However that only does a very weak encryption. It only does a type 7 encryption which is incredibly easy to decipher. A quick google search and you'll find many password decrypting tools to break those types of passwords.

A more secure password encryption is to use the `secret` command as in the following:<br>
`username TESTUSER secret TESTPASS`

This will create level 5 encryption by default and doesn't even require you to use the 'service password-encryption' command.


