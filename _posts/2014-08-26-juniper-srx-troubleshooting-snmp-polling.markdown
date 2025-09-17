---
layout: post
title: Juniper SRX troubleshooting SNMP polling
date: 2014-08-26 15:46
comments: true
categories:
- troubleshooting
- juniper
- snmp
---
Having trouble doing an SNMP walk on a Juniper SRX? Here are some troubleshooting tips to help solve the problem.

## Configure the SRX for SNMP

First things first, make sure the config is set up right on the SRX so it's accepting SNMP polling.


```
set snmp name SRX-HOSTNAME
set snmp community myCommunityString authorization read-write
set snmp community myCommunityString clients 10.1.60.0/24
set security zones security-zone TRUST interfaces ge-0/0/0.0 host-inbound-traffic system-services snmp
```

In these commands we set an SNMP name, then give permission for the 10.1.60.0/24 network to do SNMP polling. We are also setting the community string to 'myCommunityString.'

After this is configured we should be able to get on a linux device in the 10.1.60.0/24 network and issue the following command:

`snmpwalk -c myCommunityString -v 2c 192.168.55.55`

A large amount of data should come back listing all of the SNMP data. If that didn't work try some of the troubleshooting tips below.

## SNMP walk yourself

The SRX has the ability to do an SNMP poll on itself with the following command

```
show snmp mib walk jnxMibs
```

This command should spit out a lot of data showing all of the jnxMibs in the database. [Juniper has documentation if this doesn't poll](http://www.juniper.net/techpubs/en_US/junos12.3/topics/reference/general/snmp-junos-faq.html):

What can I do if the MIB OID query is not responding?

There can be various reasons why the MIB OID query stops responding. One reason could be that the MIB itself is unresponsive. To verify that the MIB responds, use the show snmp mib walk | get MIB name | MIB OID command:

> If the MIB responds, the communication issue exists between the SNMP master and SNMP agent. Possible reasons for this issue include network issues, an incorrect community configuration, an incorrect SNMP configuration, and so on.
> If the MIB does not respond, enable SNMP traceoptions to log PDUs and errors. All incoming and outgoing SNMP PDUs are logged. Check the traceoptions output to see if there are any errors.

> If you continue to have problems with the MIB OID query, technical product support is available through the Juniper Networks Technical Assistance Center (JTAC).


## Turn on Traceoptions

Turning on traceoptions for SNMP will help see what the SRX is doing when it gets the SNMP poll request. Turn on traceoptions with the following edit commands:

```
edit snmp traceoptions
set file trace_snmp
set flag all
```

Try your SNMP walk again from a linux machine and then take a look at the log with `show log trace_snmp`.

**If** you don't see anything showing up in the trace options there's one of two things wrong.

* Security settings are restricting SNMP polls from coming into the interface you're coming in on. Verify which interface you're polling and check that host-inbound-traffic is permitted. See the Configure SNMP section at the top of this blog post to understand that command.
* The SNMP poll may not even be arriving at the SRX. It's possible the SNMP poll is being blocked somewhere or doesn't know how to make it to the SRX. Verify routing is working correctly and that port 161 isn't blocked.

**If** you are seeing some stuff happening in the logs when trying the SNMP walk command take a look at the following to understand the output.

### Understanding the snmp traceoptions results

Let's look at a successfull SNMP poll that shows up in the traceoptions log.

```
Aug 25 20:49:29 snmpd[5f07] >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
Aug 25 20:49:29 snmpd[5f07] >>> Get-Request
Aug 25 20:49:29 snmpd[5f07] >>>  Source:      10.1.60.200
Aug 25 20:49:29 snmpd[5f07] >>>  Destination: 192.168.55.55
Aug 25 20:49:29 snmpd[5f07] >>>  Version:     SNMPv2
Aug 25 20:49:29 snmpd[5f07] >>>  Request_id:  0x5f07
Aug 25 20:49:29 snmpd[5f07] >>>  Community:   myCommunityString
Aug 25 20:49:29 snmpd[5f07] >>>  Error:       status=0 / vb_index=0
Aug 25 20:49:29 snmpd[5f07] >>>   OID  : ifOperStatus.578
Aug 25 20:49:29 snmpd[5f07] >>>   OID  : ifName.578
Aug 25 20:49:29 snmpd[5f07] >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
Aug 25 20:49:29 jnx_ifEntry_stat_actual_lookup: sync request for ae0.50
Aug 25 20:49:29 jnx_ifEntry_stat_actual_lookup: sync request for ae0.50
Aug 25 20:49:29 snmpd[5f07]  <<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<
Aug 25 20:49:29 snmpd[5f07]  <<< Get-Response
Aug 25 20:49:29 snmpd[5f07]  <<<  Source:      10.1.60.200
Aug 25 20:49:29 snmpd[5f07]  <<<  Destination: 192.168.55.55
Aug 25 20:49:29 snmpd[5f07]  <<<  Version:     SNMPv2
Aug 25 20:49:29 snmpd[5f07]  <<<  Request_id:  0x5f07
Aug 25 20:49:29 snmpd[5f07]  <<<  Community:   myCommunityString
Aug 25 20:49:29 snmpd[5f07]  <<<  Error:       status=0 / vb_index=0
Aug 25 20:49:29 snmpd[5f07]  <<<
Aug 25 20:49:29 snmpd[5f07]  <<<   OID  : ifOperStatus.578
Aug 25 20:49:29 snmpd[5f07]  <<<   type : Number
Aug 25 20:49:29 snmpd[5f07]  <<<   value: 1
Aug 25 20:49:29 snmpd[5f07]  <<<
Aug 25 20:49:29 snmpd[5f07]  <<<   OID  : ifName.578
Aug 25 20:49:29 snmpd[5f07]  <<<   type : OctetString
Aug 25 20:49:29 snmpd[5f07]  <<<   value: "ae0.50"
Aug 25 20:49:29 snmpd[5f07]  <<<   HEX  : 61 65 30 2e  35 30
Aug 25 20:49:29 snmpd[5f07]  <<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<
```

When there are `>>>` symbols it means that was an incoming SNMP request. When there are `<<<` it means that's a SNMP response. In the case above we see the SNMP request come in from 10.1.60.200 going to the SRX at 192.168.55.55 using SNMP string myCommunityString. Within the same second we see the SRX responding with the information requested in the SNMP poll.

If the logs are showing the request but no response it may mean that the SNMP configuration doesn't have this source IP listed in the client. See the section on configuring SNMP at the top of this post to verify the network is present.
