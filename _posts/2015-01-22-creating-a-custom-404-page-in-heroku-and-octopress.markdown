---
layout: post
title: Creating a custom 404 page in Heroku and Octopress
date: 2015-01-22 21:26
comments: true
categories:
- octopress
- heroku
- coding
---
A 404 page not found error occurs when the webserver cannot find the webpage or URL the user requested. Here are some benefits to having an error 404 not found page on your website:

* Collect analytics on pages people are trying to go to. This can be helpful for tracking down broken links or interest that people have on your site.
* Give users a link to popular content to try to keep them on your site.
* Improves usability of the site.
* Can turn a bad experience into a better one.


## Creating a 404 page on Heroku
Creating a custom error 404 page in Heroku is very simple. By default if Heroku can't find the page it will first try to display /404.html and if it can't find that then it'll display a blank page.

So to create a custom 404 page in Heroku simply create a 404.html page and make sure it is available by simply going to `example.com/404.html`.


## Creating the 404 page in Octopress
If using Octopress you can create the page /source/404.html and simply put something like "the page cannot be found" in this file. [See here](http://404notfound.fr/) for inspiration on nifty looking 404 pages. If you don't want your search engines caching your 404 page or knowing about it, use [this guide](https://kaworu.ch/blog/2013/05/25/custom-404-page-with-octopress/) to make those exempt. Just make sure to keep the location of your 404 page at /source/404.html so Heroku can find it.

Push your blog to Heroku and verify by going to a URL that doesn't work. 






