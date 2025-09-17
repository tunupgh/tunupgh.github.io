---
layout: post
title: Cisco ASA Max ACL Limit
date: 2015-10-18 16:16
comments: true
categories:
- cisco
- scripts
- asa
- acl
---
The Cisco ASA firewall doesn't have any hard limits for the number of Access Control Entries (ACEs). However, this is bound by the memory of the model. Each ACE uses at least 212 bytes of RAM.

Once you reach or get close to the maximum number of ACEs, the performance of the ASA decreases by 10-15%.

Use this table below to stay within the maximum number of allowed Access List Entries.

An easy trick to find out how many ACEs an ACL has is to use this command: `show access-list | include elements`.


<table class="table">
		<tr>
			<td><strong>Model</strong></td>
			<td><strong>Max Recommended ACEs</strong></td>
			<td><strong>Tested ACEs</strong></td>
		</tr>

	<tr>
		<td>5505</td>
		<td>25k</td>
		<td></td>
	</tr>

	<tr>
		<td>5510</td>
		<td>80k</td>
		<td>80k</td>
	</tr>
	<tr>
		<td>5512-X</td>
		<td>100k</td>
		<td></td>
	</tr>
	<tr>
		<td>5515-X</td>
		<td>100k</td>
		<td></td>
	</tr>
	<tr>
		<td>5520</td>
		<td>200k</td>
		<td>300k</td>
	</tr>
	<tr>
		<td>5525-X</td>
		<td>200k</td>
		<td></td>
	</tr>
	<tr>
		<td>5540</td>
		<td>500k</td>
		<td>700k</td>
	</tr>
	<tr>
		<td>5545-X</td>
		<td>300k</td>
		<td></td>
	</tr>
	<tr>
		<td>5550</td>
		<td>700k</td>
		<td>700k</td>
	</tr>
	<tr>
		<td>5555-X</td>
		<td>500k</td>
		<td></td>
	</tr>
	<tr>
		<td>5580</td>
		<td>750k</td>
		<td>1 mil+</td>
	</tr>
	<tr>
		<td>5585 10/20/40/60</td>
		<td>500k/750k/1 mil/2 mil</td>
		<td>500k/750k/1 mil/2 mil</td>
	</tr>
	<tr>
		<td>ASA SM</td>
		<td>2 mil</td>
		<td>2 mil</td>
	</tr>


</table>


Source: Cisco Live! 2014 presentation.



