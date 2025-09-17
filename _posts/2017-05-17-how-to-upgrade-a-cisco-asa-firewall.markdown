---
layout: post
title: How to Upgrade a Cisco ASA Firewall
date: 2017-05-17 22:40
comments: true
categories:
- cisco
- asa
- scripts
- upgrade
---
To upgrade the OS of a Cisco ASA firewall follow these basic steps:

1. Download Software
1. Get Software on ASA
1. Verify Software
1. Configure ASA
1. Reboot ASA

### Download Software

The general suggestion is to run the latest version of ASA OS version that the ASA supports. The OS image contains the entire OS. There are no such things as patches or updates. You download a whole new OS to be installed. Because of this there usually isn't a need to do a stepped upgrade. The only time you need to do a [stepped upgrade is when you're upgrading from a very old version (pre 9.1)](/cisco-asa-upgrade-path/) and want the config to be migrated for you. 

Go to the downloads section of Cisco.com (requires Cisco login with valid support contract): [https://software.cisco.com/download/](https://software.cisco.com/download)

**ASA Without FirePower IPS**

Navigate to **Security** > **Firewalls** > **Adaptive Security Appliace (ASA)** > **ASA Model** > **Software on Chassis** > **Adaptive Security Appliance (ASA) Software**. 

**ASA With FirePower IPS**

Navigate to **Security** > **Firewalls** > **Next-Generation Firewalls (NGFW)** > **ASA 5500-X with FirePOWER Services** > **Model of ASA** > **Adaptive Security Appliance (ASA) Software**


> When downloading the software, hover over the image on the downloads page to see the Checksum. Copy this checksum as we'll use it to verify the image later.

#### Understanding Versions 


Unlike a Cisco Router image, the ASA image contains all features and requires additional licenses to unlock the features. This makes choosing the image a lot easier but makes handling licensing harder.

The OS image file will look like one of these 3:

`asa933-7-lfbff-k8.SPA`<br>
`asa924-5-smp-K8.bin`<br>
`asa924-5-k8.bin`

The numbers indicate the version. For instance the first file here is for ASA OS Version 9.3(3)7.

The `lfbff` and `SPA` indicates it has FirePower IPS included in the image and this image is digitally signed which makes it tamper resistant.

The `smp` indicates the image is for a multi-core ASA (check how many cores using show ver).

The 3rd one is for old ASAs that have a single core. 

The `k8` tag indicates this image supports DES encryption. With a license, you can make the ASA support AES and 3DES. 

These images aren't tied to a model number, so the image downloaded for a 5512x can also be used on a 5516x. 


## Get Software on ASA
Once you have downloaded the image, upload it to the ASA. 

Upload the image to an http or ftp server and copy the image to the ASA from the ASA command line with one of these commands:

`copy http flash`

`copy ftp flash`

`copy tftp flash`

You can also use a USB flash drive to put the image on and insert it into the ASA. 

`dir /all` will show you all the files on all disks

`copy disk1:/filename flash`

It is also possible to upload the image using the ASDM.

## Verify Software
Now that the software is on the ASA you want to verify it got there without any errors. To do this, use the `verify` CLI command:

`verify disk0:/asaxxxxxxxx.xxx`

Now compare the checksum output to the checksum you saw on the downloads page from Cisco.com. If they match then this image is not corrupt.


## Configure ASA
Apply the image to the ASA with the `boot system` command like this:

`boot system disk0:/asa962-13-lfbff-k8.SPA`

This will tell the ASA to boot to that image the next time there is a reboot.


## Reboot ASA

Lastly, reboot the ASA. When it comes back up, check the version doing a `show ver`. 






