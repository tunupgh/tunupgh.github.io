---
layout: post
title: Canonical and DNS settings for www, non-www, and https for Heroku and Cloudflare
date: 2014-12-27 23:29
comments: true
categories:
- coding
- webdev
- dns
- canonical
- cloudflare
---
When deploying a web application it's important that the URL responds to both www and non-www as well as both http and https. However it's also very important that only one of those is the absolute and correct one and the server forces users to only use one.

I like to use cloudflare for my DNS configurations and Heroku for my web server. These techniques can be used on any programming language such as Ruby on Rails, PHP, Meteor.js, or Django. They can also be used on any web server such as Apache, nginx, node.js. It doesn't require any .htaccess rules either, only the clever use of Heroku, Cloudflare, and HTML.

## Setting canonical
It's important to tell search engines and bots which version of your URL is the absolute and correct version. This way search engines will be pointing to the correct URL and browsers will understand which one to use too. This is important for SEO and analytics. For SEO, search engines will see both www and non-www sites as two separate sites and giving each it's own search rankings. You want to make sure that you are clumping all of your users into a single authoritative URL so the search engines can see just how popular your domain is. This authoritative URL is known as the canonical URL.

To set the canonical URL simply add this HTML code in the head of every page on your site.

```
<head>
  <link rel="canonical" href="https://example.com" />
</head>
```

Of course every page has a unique canonical URL. If you have a webpage located at `https://example.com/about.html` then your canonical URL will have the `about.html` in it.


## Responding to everything

You want your website to listen to and respond to all of the following versions of your URL.

* http://example.com
* http://www.example.com
* https://example.com
* https://www.example.com

This is important because people will be trying every one of these combinations when going to your site. Whenever I hear or see a URL I never pay attention of whether it has www or no www before it. I simply type the name_of_url.com into my browser and go. Way too often the website hasn't set up DNS correctly and I get page not found and I just think the page doesn't exist anymore and go away. This is a grave error on the webmaster to simply ignore this problem.

### Setting up Heroku

First let's set up Heroku. 

1. Go to the `settings` page on your application
1. Go to the `domains` section and add the domain you wish to be your primary one. In our case we are choosing example.com

### Setting up Cloudflare

##### DNS Settings

For setting up Heroku and Cloudflare simply do the following DNS settings:

```
CNAME - example.com - is an alias of example.herokuapp.com
CNAME - * - is an alias of example.herokuapp.com
CNAME - www - is an alias of example.herokuapp.com
```

#### Forwarding settings

Now we need to set a [forwarding rule in Cloudflare](https://support.cloudflare.com/hc/en-us/articles/200172286-How-do-I-do-url-forwarding-with-CloudFlare-).

1. Go to the gear icon to open your `settings`.
2. Go to the `Page Rule's` option
3. Click `Forwarding` `ON`.
4. In the `URL pattern` box, type `www.example.com/*`
5. In the `forwarding destination URL` box, type `https://example.com`
6. Choose `forwarding type` `301 - permanent`

If you don't wish to or can't use https then you can just choose http here instead.

If you do wish to use https then I also recommending setting `always use https` in the page rules also.

## Responding to HTTPS

There is a really cool trick to getting free https when using both free Cloudflare and free Heroku plans. Your Heroku app should already be responding to https://example.heroku.com. It has a it's own official HTTPS certificate that you can use if you use their domain name. Cloudflare has an option called Full SSL which is part of the free plan. This setting will allow HTTPS connections to Cloudflare, then do a HTTPS connection to the hosting domain. Essentially it's end to end HTTPS all for free.

#### Setting up Full SSL in Cloudflare
Access your Cloudflare account and do the following:

1. Go to the gear and choose `Cloudflare settings`.
1. Scroll down to `SSL` and choose `Full SSL`.

Cloudflare is updated upon changing this setting. 


## Conclusion

Now your website should be responding to each of the following variations of your site:

* http://www.example.com
* http://example.com
* https://www.example.com
* https://example.com

However, the first three variations should all be forwarding to `https://example.com`. This is done using the Cloudflare forwarding rules we set.

Lastly, search engines and browsers will understand your canonical settings you have defined and only be using that version of your URL as the authoritative URL to use.





