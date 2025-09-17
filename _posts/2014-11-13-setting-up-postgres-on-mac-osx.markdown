---
layout: post
title: Setting up PostgreSQL on Mac OSX
date: 2014-11-13 22:03
comments: true
categories:
- postgres
- misc
- mac
---
Here are the steps I used to set up Postgres in Mac OSX. Most of what is written here was learned from [this blog post](http://blog.willj.net/2011/05/31/setting-up-postgresql-for-ruby-on-rails-development-on-os-x/). I am saving my notes here in case that site gets removed.

### Install Postgress using brew
If you don't have [homebrew](http://brew.sh/), install it first. Then simply run the command:

`brew install postgres`

### Initialize Postgres
This command initializes the database.

`initdb /usr/local/var/postgres`

### Set up Postgres to run at startup

Check if the directory `~/Library/LaunchAgents` exists. Create it if it doesn't exist.

Find the plist file that came with the postgres install. I found mine at `/usr/local/Cellar/postgresql/9.3.4/homebrew.mxcl.postgresql.plist`.

Copy the plist file to the LaunchAgents directory.

`cp /usr/local/Cellar/postgresql/9.3.4/homebrew.mxcl.postgresql.plist ~/Library/LaunchAgents/`

Now use launchctl to load the file using this command:

`launchctl load -w homebrew.mxcl.postgresql.plist`

Now when the computer reboots, postgres will automatically startup.

## Additional Postgres Commands

### Create a user

Postgres has a shell command called `createuser` which will create a user for Postgres. Use it right from the OSX terminal command line like this:

`createuser --pwprompt tunnelsup`

If it asks you questions about the user you can say 'n' for all of the questions.

If you wish to create a user without a password just take the `--pwprompt` off the command.

### Create a database

Here is the command:

`createdb -Otunnelsup -Eutf8 mysite_development`

The `-O` indicates the user that will become the owner of the database.

### Access the Database

The following command can be used to log into the database.

`psql -U tunnelsup -W mysite_development`

The -U means to login using that username and the -W means to prompt for a password.

To exit the shell hit `Ctrl`+`d`


## Starting a new Rails project

In case you're using this guide to get started using rails here's how I would start the new rails app.

```
# Create the rails project
rails new projectname --database=postgresql
cd projectname
# Create the Postgres user
createuser projectname
# Create the databases
createdb -Oprojectname -Eutf8 projectname_development
createdb -Oprojectname -Eutf8 projectname_test
# Create some models/controllers etc
rails generate controller site
rails generate model 
# Run migrations
rake db:migrate
# Start the server
rails server
```

In dev I may not use a password for the database user. This makes it easier for me to get started and I don't even have to edit the database.yml file. 


