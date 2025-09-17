---
layout: post
title: How to determine the current EPS for Cisco MARS
date: 2015-09-04 11:08
comments: true
categories:
- cisco
- mars
- scripts
---
There are two ways to determine the events per second for a Cisco MARS SIEM system.


### See the current EPS the system is processing

You can see the real time EPS the system is currently handling by looking in the logs. This is found under Admin > System Maintenance > View Log Files.

Once there, expand the time frame and search for something that looks like this:

```
Source: ./pnparser
2016:message rate: 1068.164429 msgs/sec, total: 2147550000 msgs, total avg rate: 420.480804 msgs/sec
```

The `total avg rate` is the current EPS the system is processing.


### Calculate it based on events per month

On the Summary screen there is a table on the top left side that says how many events have been received. You can change this to month and then divide the value by the number of seconds in a month.

In my case I see there are `1,137,479,506` events for the last 30 days. I divide this by `2,592,000` which is how many seconds are in 30 days. This leaves me with `438 EPS`.