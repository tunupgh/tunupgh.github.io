---
layout: post
title: Juniper front panel LED color meanings
date: 2013-06-18 16:24
comments: true
categories:
- juniper
- troubleshooting
---
<div class="entry-content">
  <style>
     table.ztab1 {
        margin: auto;
        width:400px;
        border: 1px solid black;
      }
	 table.caption {
        font-weight: bold;
        background-color: #FFFFFF;
      }
      table.ztab1 th {
        font-weight: bold;
        text-align:left;
        background-color: #AAAAAA;
        border-bottom: 2px solid black;
      }

      table.ztab1 th,td {
        padding: 4px 5px;
      }

      table.ztab1 tr:nth-of-type(odd) {
        background-color: #FFFFFF;
      }

      table.ztab1 tr:nth-of-type(even) {
        background-color: #CCCCCC;
      }
  </style>
</div>


Here is a key to the color codes on a Juniper SRX. You can determine what LEDs are turned on by either looking at the front panel of the SRX or doing a `show chassis craft-interface` command.

## LEDs on front panel of an SRX

<table class="ztab1">
	<caption>Alarm LED</caption>
	<thead>
		<tr>
			<th>Color</th>
			<th>Description</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>Red steady</td>
			<td>critical alarm</td>
		</tr>
		<tr>
			<td>Amber steady</td>
			<td>major alarm</td>
		</tr>
		<tr>
			<td>Green</td>
			<td>no alarms</td>
		</tr>
		<tr>
			<td>Off</td>
			<td>device powered off</td>
		</tr>
	</tbody>
</table>

<br><br>
<table class="ztab1">
	<caption>Status LED</caption>
	<thead>
		<tr>
			<th>Color</th>
			<th>Description</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>Green steady</td>
			<td>normal</td>
		</tr>
		<tr>
			<td>Amber steady</td>
			<td>startup</td>
		</tr>
		<tr>
			<td>Red steady</td>
			<td>device failed</td>
		</tr>
		<tr>
			<td>Off</td>
			<td>device powered off</td>
		</tr>
	</tbody>
</table>
<br><br>
<table class="ztab1">
	<caption>Power LED</caption>
	<thead>
		<tr>
			<th>Color</th>
			<th>Description</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>Green steady</td>
			<td>normal</td>
		</tr>
		<tr>
			<td>Amber steady</td>
			<td>device is gracefully shutting down</td>
		</tr>
		<tr>
			<td>Red steady</td>
			<td>power supply problem</td>
		</tr>
		<tr>
			<td>Off</td>
			<td>device powered off</td>
		</tr>
	</tbody>
</table>
<br><br>

## LEDs on the front panel of a Netscreen
<br>
<table class="ztab1">
	<caption>CPU Utilization</caption>
	<thead>
		<tr>
			<th>Color</th>
			<th>Description</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>Green steady</td>
			<td>Current level of CPU use</td>
		</tr>
		<tr>
			<td>Off</td>
			<td>Less than 5% CPU use</td>
		</tr>
	</tbody>
</table>
<br><br>
<table class="ztab1">
	<caption>PWR LED</caption>
	<thead>
		<tr>
			<th>Color</th>
			<th>Description</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>Green steady</td>
			<td>System is receiving power</td>
		</tr>
		<tr>
			<td>Off</td>
			<td>Power is not on</td>
		</tr>
		<tr>
			<td>Red solid</td>
			<td>power supply problem</td>
		</tr>
	</tbody>
</table><br><br>
<table class="ztab1">
	<caption>Status LED</caption>
	<thead>
		<tr>
			<th>Color</th>
			<th>Description</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>Green Blinking</td>
			<td>System is receiving power</td>
		</tr>
		<tr>
			<td></td>
			<td>device is gracefully shutting down</td>
		</tr>
		<tr>
			<td>Red steady</td>
			<td>power supply problem</td>
		</tr>
		<tr>
			<td>Off</td>
			<td>device powered off</td>
		</tr>
	</tbody>
</table><br><br>
<table class="ztab1">
	<caption>HA</caption>
	<thead>
		<tr>
			<th>Color</th>
			<th>Description</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>Green steady</td>
			<td>Indicates this module is the master</td>
		</tr>
		<tr>
			<td>Amber solid</td>
			<td>Indicates this module is a slave</td>
		</tr>
		<tr>
			<td>Off</td>
			<td>Indicates no HA activity seen</td>
		</tr>
	</tbody>
</table><br><br>
<table class="ztab1">
	<caption>Alarm</caption>
	<thead>
		<tr>
			<th>Color</th>
			<th>Description</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>Red solid</td>
			<td> indicates a critical alarm which could mean the system crash or failure</td>
		</tr>
		<tr>
			<td>Red blinking</td>
			<td>Indicates that an alarm has been triggered</td>
		</tr>
		<tr>
			<td>Amber solid</td>
			<td>indicates a major alarm which could be low memory</td>
		</tr>
		<tr>
			<td>Off</td>
			<td>No alarms detected</td>
		</tr>
	</tbody>
</table><br><br>
<table class="ztab1">
	<caption>Flash</caption>
	<thead>
		<tr>
			<th>Color</th>
			<th>Description</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>Green Solid</td>
			<td>Flash card is installed</td>
		</tr>
		<tr>
			<td>Green blinking</td>
			<td>Indicates flash card activity</td>
		</tr>
		<tr>
			<td>Red Blinking</td>
			<td>Flash is completely used or error has occurred with it</td>
		</tr>
		<tr>
			<td>Off</td>
			<td>No flash card detected in slot</td>
		</tr>
	</tbody>
</table>