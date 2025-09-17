---
layout: post
title: Installing Cisco CX ASA
date: 2013-07-13 10:39
comments: true
categories:
- cisco
- asa
- cx
- nextgen
- scripts
---
The Cisco ASA 5500-X series firewalls can utilize the new CX features that will enable it to be a "next gen firewall" or "context aware".

Installing CX requires a few unique commands. 

## Before You Begin

- Requires ASA 5500-X firewall. 
- Requires additional hard drive. Part number: ASA5500X-SSD120. A single hard drive will work or a pair can be installed if the ASA can take multiple hard drives and you want RAID support.
- Requires additional CX license which is activated within PRSM
- Requires additional PRSM license. This is used to manage the CX ASA.
- Requires ASA version 9.1.1+
- Requires 3Gig of space on the ASA flash card. (Uncertain if this is required only for install or always)
- Cannot be ran concurrently with software IPS. (May be supported in future versions)

- If installing on the 5585 a line card is required instead of a SSD drive and the card must be in the top slot.

### Download Software

Acquire the ASA CX boot image from cisco.com. It will look something like this: `asacx-5500x-boot-9.1.1.img`. 

Also acquire the system image. This will look something like this: `asacx-sys-9.1.1.pkg`.

Please note, the 5500-X platforms all use the same boot image but the 5585 uses a different one. 


## Installing ASA CX 




1. Copy CX Boot image to ASA flash.

    `copy http://<server>/asacx-5500x-boot-9.1.1.img flash`

1. Configure the Boot Image to be used on ASA.

	`sw-module module cxsc recover configure image disk0:/asacx-5500x-boot-9.1.1.img`

1. Boot the image from ASA.

	`sw-module module cxsc recover boot`

1. Session into the CX module.

	`session cxsc console`

1. Configure the CX disk partitions.

	console:  `partition`

1. Setup the CX management IP.
	
	console: `setup`

1. Install complete CX system package from CX console and reboot.

	console: `system install http://<server>/<path>/asacx-sys-9.1.1.pkg`

## Installing PRSM

PRSM is the Prime Security Manager. It is required to manage and control the CX ASA. Only system maintenance can be done from the console. All policies are managed through PRSM. PRSM can only manage 20 CX devices at a time. 

PRSM can run on the ASA itself or on a dedicated server.

Refer to cisco documentation for further details on installing PRSM.
[http://www.cisco.com/en/US/docs/security/asacx/9.0/installation/guide/b_Installation_Guide_for_PRSM_9_0.pdf](http://www.cisco.com/en/US/docs/security/asacx/9.0/installation/guide/b_Installation_Guide_for_PRSM_9_0.pdf)

