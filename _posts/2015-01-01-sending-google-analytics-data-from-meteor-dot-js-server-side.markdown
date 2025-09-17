---
layout: post
title: Sending Google Analytics data from Meteor.js server side
date: 2015-01-01 15:50
comments: true
categories:
- meteor
- coding
- webdev
---
Recently I've created a website that responds with HTML and also has an API that responds with JSON data. The website was create in Meteor.js and I wanted to integrate it into Google Analytics. Having it send Google Analytics statistics on the client side was easy and straight forward. However when it came time to send statistics to Google Analytics from the Meteor.js server side of the application I had to spend quite a bit of time finding an answer.

## Use the Google Analytics Measurements Protocol
Check out the Google Developers section on Google Analytics regarding the Measurements Protocol.

https://developers.google.com/analytics/devguides/collection/protocol/v1/devguide#page

## Use the Meteor HTTP.call() function

http://docs.meteor.com/#/full/http_call

Don't forget, as of Meteor 1.2 this isn't on by default. It needs to be added by doing the following from the command prompt:

`meteor add http`

## Putting it all together

Here are the results of my code in order to trigger a page view every time the user visits the /api route of my web app.


```javascript
if (Meteor.isServer) {
  Meteor.startup(function () {
    Router.map(function() {
        this.route('methodExample', {
            path: '/api',
            where: 'server',
            action: function() {

            	// Send Google Analytics
                HTTP.call("POST", "https://www.google-analytics.com/collect?",
                           {params: {
                              'v': '1',
                              'tid': 'UA-XXXXXX-Y',
                              'cid': '555',
                              't': 'pageview',
                              'dp': 'api'
                            }}
                );
            }
        });
    });
  });
}
```




