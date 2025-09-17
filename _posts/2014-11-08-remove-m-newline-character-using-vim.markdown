---
layout: post
title: Remove ^M Newline Character using vim
date: 2014-11-08 18:48
comments: true
categories:
- misc
- vim
---
Sometimes when opening a file in vim there are a lot of `^M` characters at the end of everyline. This is probably because the file was saved in Windows which uses a slightly different newline, linebreak, carriage return, and line feed characters. It may look something like this:

<pre><code>Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod<span style="color: #FF6666;">^M</span>
tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam,<span style="color: #FF6666;">^M</span>
quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo<span style="color: #FF6666;">^M</span>
consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse<span style="color: #FF6666;">^M</span>
cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non<span style="color: #FF6666;">^M</span>
proident, sunt in culpa qui officia deserunt mollit anim id est laborum.<span style="color: #FF6666;">^M</span></code></pre>

## Using Vim to remove the ^M characters

The fasttest way to remove these characters in VIM is to do the following:

`:%s/<Cntrl+V><Cntrl+M>/<Cntrl+V><Cntrl+M>/g`

This command cannot be pasted directly into Vim. You must press `Control`+`V` then `Control`+`M` to get the special character.


The `:%s` is the substitution command. If you had `:%s/this/that/` it would read substitute the word `this` with the word `that`.

The `Control`+`V` `Control`+`M` is using a special code to tell Vim to create the character we are looking for.

The `/<Cntrl+V><Cntrl+M>/` means we are substituting it with a carriage return and line break that our system recognizes.

The `g` indicates to execute this substitution globally on every occurance.

