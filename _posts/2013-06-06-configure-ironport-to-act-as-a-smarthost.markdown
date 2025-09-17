---
layout: post
title: Configure Ironport to act as a smarthost
date: 2013-06-06 09:41
comments: true
categories:
- ironport
- scripts
- smarthost
- email
- esa
- cisco
---
Ironport Email Appliance (ESA) can act as a smarthost to relay email outbound to the internet. The trick to doing this is to have two interfaces on the Ironport, one acting as incoming from the internet and one acting as incoming from the exchange server. Here is how to configure the internal interface to relay mail from exchange.

1. Click on 'Add Listener'

2. Name the listener 'OutboundMail'

3. For 'Type of Listener' select 'Private'

4. Submit this page

5. Click 'HAT' link for the new 'OutboundMail' listener

6. Click on 'RELAYLIST'

7. Click on 'Add Sender' and add the IP address(es) of your mail servers to this sender group.  This allows only the IP addresses that you specify to relay, and ensures that they are treated as outgoing senders.

8. Submit this page

9. Commit the changes
