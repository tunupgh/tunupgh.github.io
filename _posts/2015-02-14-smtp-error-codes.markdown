---
layout: post
title: SMTP Error Codes
date: 2015-02-14 11:05
comments: true
categories:
- smtp
- mail
- postfix
- misc
---
The following table displays the SMTP error or response codes. This is sometimes written like "512" and sometimes written like "5.1.2". These two ways of writing the code are synonymous. 

<table class="table">
	<thead>
		<th>Code</th>
		<th>Meaning</th>
	</thead>
	<tbody>
		<tr>
			<td>211</td>
			<td>Indicates the system status or system help reply</td>
		</tr>
		<tr>
			<td>214</td>
			<td>Help message</td>
		</tr>
		<tr>
			<td>220</td>
			<td>Service ready</td>
		</tr>
		<tr>
			<td>221</td>
			<td>Service closing transmission channel</td>
		</tr>
		<tr>
			<td>250</td>
			<td>Requested mail action ok, completed</td>
		</tr>
		<tr>
			<td>251</td>
			<td>User not local</td>
		</tr>
		<tr>
			<td>252</td>
			<td>Cannot VRFY user, but will still accept message and attempt to deliver</td>
		</tr>
		<tr>
			<td>354</td>
			<td>Start mail input</td>
		</tr>
		<tr>
			<td>421</td>
			<td>Service not available, closing transmission channel</td>
		</tr>
		<tr>
			<td>450</td>
			<td>Requested mail action not taken: mailbox unavailable</td>
		</tr>
		<tr>
			<td>451</td>
			<td><a href="/smtp-error-451/">Requested mail action aborted: local error</a></td>
		</tr>
		<tr>
			<td>452</td>
			<td>Requested action not taken: insufficient storage</td>
		</tr>
		<tr>
			<td>500</td>
			<td>Syntax error - command not recognized</td>
		</tr>
		<tr>
			<td>501</td>
			<td>Syntax error in command, parameters, or message fields</td>
		</tr>
		<tr>
			<td>502</td>
			<td>Command not implemented</td>
		</tr>
		<tr>
			<td>503</td>
			<td>Bad sequence of commands or authentication required to use command</td>
		</tr>
		<tr>
			<td>504</td>
			<td>Command parameter not implemented</td>
		</tr>
		<tr>
			<td>510</td>
			<td>Bad email address</td>
		</tr>		
		<tr>
			<td>511</td>
			<td>Bad email address</td>
		</tr>
		<tr>
			<td>512</td>
			<td>Unable to resolve recipient domain (DNS error)</td>
		</tr>
		<tr>
			<td>513</td>
			<td>Address type is incorrect</td>
		</tr>
		<tr>
			<td>523</td>
			<td>Message too large for recipient's mailbox</td>
		</tr>

		<tr>
			<td>530</td>
			<td>Authentication required or server blacklisted by recipient or no such recipient mailbox</td>
		</tr>
		<tr>
			<td>541</td>
			<td>Recipient address rejected - blocked by antispam, intrusion prevention, or mail filtering</td>
		</tr>
		<tr>
			<td>550</td>
			<td><a href="/smtp-error-550/">Recipient mailbox unavailable</a></td>
		</tr>
		<tr>
			<td>551</td>
			<td>Invalid recipient address; relay denied</td>
		</tr>
		<tr>
			<td>552</td>
			<td>Size of incoming message exceeds storage limit</td>
		</tr>
		<tr>
			<td>553</td>
			<td>Mailbox name invalid or authentication required or reverse DNS lookup failure</td>
		</tr>
		<tr>
			<td>554</td>
			<td>Transaction failed - address doesn't exist or rejected due to blacklist</td>
		</tr>

	</tbody>
</table>



#### Further reading

Official documentation:<br>
https://www.iana.org/assignments/smtp-enhanced-status-codes/smtp-enhanced-status-codes.xhtml

More helpful information for each reply code:<br>
http://www.answersthatwork.com/Download_Area/ATW_Library/Networking/Network__3-SMTP_Server_Status_Codes_and_SMTP_Error_Codes.pdf
