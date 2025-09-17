---
layout: post
title: MARS hard drive issues
date: 2013-06-06 10:52
comments: true
categories:
- mars
- cisco
- hard drive
- hdd
- hotswap
- troubleshooting
---
### Possible results from the `raidstatus` command for MARS 55, 110R, 110, 210, GC2R, and GC2
Use the `raidstatus` CLI command to view the status of the RAID array (virtual disk) and of the individual HDDs.

**Status:** Failed<br>
**Possible Cause:** Unrecoverable error on previously operative HDD.<br>
**Recommended Action:** Hotswap with a new HDD.

**Status:** Offline<br>
**Possible Cause:** The hotswap remove command was executed for this HDD.<br>
**Recommended Action:** Execute a hotswap add on the HDD if the HDD is known to be good.

**Status:** Unconfigured Good<br>
**Possible Cause:** An online HDD was removed and inserted without executing a hotswap command sequence.<br>
**Recommended Action:** Execute a hotswap remove and hotswap add on the HDD.

**Status:** Unconfigured Bad<br>
**Possible Cause:** An online HDD was removed or inserted without executing a hotswap sequence and the HDD has a media error.<br>
**Recommended Action:** Hotswap with a new HDD.

**Status:** N/A<br>
**Possible Cause:** The HDD slot is empty.<br>
**Recommended Action:** Insert a new HDD with the hotswap add command


### Procedure to hotswap a MARS hard drive

This section pertains only to the MARS 55, 110R, 110, 210, GC2R, and GC2 appliances.
An HDD can be hotswapped, that is, replaced without rebooting the MARS appliance. The hotswap
actions can be summarized in the following five steps. The detailed procedure is in the section,
Procedure to Hotswap a Hard Drive.

1. Establish a console connection to the MARS appliance.
2. Enter the `raidstatus` command to determine the status and the chassis HDD slot number of the HDD to hotswap.
3. Execute a `hotswap remove <disk>` command, then remove the HDD.
4. Execute a `hotswap add <disk>` command then insert the replacement HDD.
5. Enter the `raidstatus` command to monitor the progress of the replacement HDD as it is rebuilt.


---

Source: [http://www.cisco.com/en/US/docs/security/security_management/cs-mars/6.0/hardware/installation/guide/hig_mars_6x.pdf](http://www.cisco.com/en/US/docs/security/security_management/cs-mars/6.0/hardware/installation/guide/hig_mars_6x.pdf)
