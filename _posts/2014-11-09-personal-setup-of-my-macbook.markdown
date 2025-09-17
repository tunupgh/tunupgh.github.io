---
layout: post
title: Personal setup of my Macbook
date: 2014-11-09 15:52
comments: true
categories:
- mac
- misc
---
There are certain things I need to do to a Mac before it feels like home to me. Here is my personal setup.

#### Configuring external keyboard and mouse
So I don't go totally crazy I like to try to have both my Windows and Mac experiences somewhat similar when it comes to mouse and keyboard usage. I use the Logitech M705 mouse. I love the infinite scroll, multiple buttons, and 3 year battery life.  

* Installed Logitech unifying software to connect the mouse but not the Logitech control center.
* Purchased and installed [Steermouse](http://plentycom.jp/en/steermouse/index.html) software to give me control over the mouse. 
* Installed [Scroll Reverser](http://www.macupdate.com/app/mac/37872/scroll-reverser) so that my mouse scrolls inverted and my trackpad scrolls natural.
* Changed default behavior of end/home keys. I think by default it goes to end of line but I want it to go to end of file or page down. Config found in: /Users/username/Library/KeyBindings/DefaultKeyBinding.dict
* My external keyboard has the control key in the wrong place since it's a Windows keyboard. I had to switch the alt and windows keys to be option and command using the keyboard preferences. I did this by going into the keyboard settings, then on the keyboard tab go to the modifier keys. These settings seem to know the difference between an external keyboard and the keyboard on the Macbook.
* Got rid of cntrl-cmd-D doing a dictionary lookup. Edit ~/Library/prefrences/com.apple.symboloickeys.plist (using text wrangler) and set key 70 to false. This is simply because I use this key in Sublime Text.

#### Programs 

* Installed [Alfred](http://www.alfredapp.com/) to make finding things better. Made the hotkey for Alfred be Cntrl+CMD+P.
* Installed [homebrew](http://brew.sh/) (like apt-get for Mac)
* Installed wget using brew `brew install wget`
* Installed git using brew `brew install git`
* Installed [rvm](https://rvm.io/rvm/install) for Ruby on Rails development
* [Set up Postgres](http://www.tunnelsup.com/setting-up-postgres-on-mac-osx/) for Ruby on Rails development
* Set up time machine to back up to the Synology NAS
* Purchased and installed [Parallels](http://www.parallels.com/) to run virtual machines
* Purchased and installed [Sublime Text](http://www.sublimetext.com/) to use as my main text editor
* Purchased and installed [SecureCRT](http://www.vandyke.com/products/securecrt/) to use as my terminal


