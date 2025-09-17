---
layout: post
title: How to do a password recovery on a Cisco ASA firewall
date: 2013-06-17 22:52
comments: true
categories:
- troubleshooting
- cisco
- firewall
- asa
- password recovery
---
Perhaps you've forgotten the password to your firewall? This post will answer the question: How do I restore the password on a Cisco ASA?

## Password Recovery
To recover passwords, perform the following steps:

- Console into the ASA.

- Reboot the ASA

- Press the escape key during reboot to enter ROMMON. 

- Tell the firewall to ignore the startup config on next reload:<br>
rommon #1> `confreg`

The following will be displayed:<br>
> Current Configuration Register: 0x00000011<br>
> Configuration Summary:<br>
> boot TFTP image, boot default image from Flash on netboot failure<br>
> Do you wish to change this configuration? y/n [n]:<br>

- Jot down the config register value for later.

- Enter `y` to say yes.

- Hit enter at each prompt to accept the default. When you get to "disable system configuration" hit `y`.

- Reboot the ASA<br>
rommon #2> `boot`

#### At this point the ASA should reload and completely bypass the configuration.

- When the firewall reboots it will not prompt a console user for a username and the enable password is blank. Go into enable mode.<br>
`enable`

- Restore the old config<br>
`copy startup-config running-config`

- Enter config mode and reset the password<br>
`configure terminal`<br>
`password NEW_PASSWORD`<br>
`enable password NEW_PASSWORD`<br>
`username USER password NEW_PASSWORD`<br>

- Restore the config registry to where it was to begin with. This is the number you wrote down earlier.<br>
`config-register 0x0000###`

- Save your config
`copy running-config startup-config `

At this point you have regained access to the firewall and restored the config file and registry to where it before the password reset.


## Further reading

Link to all Cisco product password recovery pages.
http://www.cisco.com/en/US/products/sw/iosswrel/ps1831/products_tech_note09186a00801746e6.shtml