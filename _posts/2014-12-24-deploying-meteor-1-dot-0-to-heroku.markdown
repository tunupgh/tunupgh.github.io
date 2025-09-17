---
layout: post
title: Deploying Meteor 1.0 to Heroku
date: 2014-12-24 18:44
comments: true
categories:
- meteor
- webdev
- coding
---
Heroku doesn't have an official document as of yet for deploying a [meteor](http://meteor.com) application on their platform. However meteor does just use node.js under the hood, so a specailly crafted buildpack can actually make it run there... for the most part.

### Meteor BuildPack Horse

https://github.com/AdmitHub/meteor-buildpack-horse

This buildpack worked for the most part.

First start by checking your application into git with the basic following:

```
git init
git add .
git commit -m 'Initial commit'
```

Then create the heroku app with this command:

```
heroku create --stack cedar --buildpack https://github.com/AdmitHub/meteor-buildpack-horse.git
```

Now create a remote for Heroku doing this:

```
heroku git:remote -a <name of heroku app>
```

Next you need to push the application by doing a git push:

```
git push heroku master
```

Now you need to set the ROOT_URL by doing this

```
heroku config:add ROOT_URL=https://<name of app>.herokuapp.com
```

Now you should be able to navigate to your URL and it should work.

This actually auto set me up for a free MongoLab account on Heroku and my database was up and running with 0 configuration. Wow!


### Meteorite Build Pack

Found here: https://github.com/oortcloud/heroku-buildpack-meteorite

This one I couldn't get to work with version 1.0.2.1. I would get errors after deploying to heroku that something went wrong with the application. 


