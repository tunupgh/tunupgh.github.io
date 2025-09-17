---
layout: post
title: Getting Postfix to send incoming mail to Rails
date: 2014-04-07 18:48
comments: true
categories:
- postfix
- coding
- scripts
- rvm
- ruby
- rails
---
Goal: Let's build a ruby on rails email web app that interfaces with postfix to send and receive email.

This is a lot harder than it sounds. I gave this a try at two different times in my life and spent hours and hours troubleshooting to finally come up with a working solution. This is probably too big of a topic for one blog post but I'm going to try to do it anyways!

This guide is using Ubuntu 12.04 as the operating system.

## Setup Ruby on Rails
Use the [standard RVM installation](https://rvm.io/rvm/install) method to install RVM + Ruby + Rails. None of these commands use `sudo`. For me this task included these commands

```
curl -L get.rvm.io | bash -s stable
source ~/.rvm/scripts/rvm
rvm requirements
rvm install ruby
rvm use 2.1.1 --default
rvm rubygems current
gem install rails
```

#### Install MySQL
This guide uses mysql as the database because that's something that is supported by both rails and postfix. So install mysql by doing:

```
sudo apt-get install mysql-server
```
Create a new user and give them permissions to edit a new database. These commands will create 2 new databases, give a user access to those databases, then allow the user to login using a password specified.

```mysql
mysql -uroot
create database projectname_development;
create database projectname_test;
grant all privileges on projectname_development.* to projectname;
grant all privileges on projectname_test.* to projectname;
grant usage on *.* to projectname@localhost identified by 'NEW_db_passw0rd';
```

#### Create a new Rails app  
Then you can go through the motions of creating the application by doing `rails new projectname -d mysql` which will create a new app and use the mysql database. You'll want to make sure the app can connect to the database so configure config/database.yml to give it the right credentials.

We'll need two models to get started; a user model and messages model. The users model will be used to lookup if this user/email address is valid later. The messages model will be where the incoming mail is stored.

To keep it simple the users model only needs a user `id` and `email_address`. 
The messages model will `belong to users` and have a `to`, `from`, `subject` and `body` field.

You might as well create the app all the way to the point where a user can login and look at their inbox. Where their inbox is simply displaying all of that user's messages.


## Create a Ruby script to handle incoming mail
Before we get going on postfix we need a place for postfix to send the mail when it comes in. To handle this I'm going to use two different scripts. The first being a lean script that is only moving the email from postfix to a resque worker queue. What is resque? It's a gem that can be installed which is great at handling background jobs. To me, handling email is a great background job it can be doing.

First let's put the gems in the `Gemfile` that we'll be using:

```
gem 'resque', :require => "resque/server"
gem 'mail', :require => false
gem 'redis', :require => false
```

We say `:require => false` because they aren't needed in the rails app but we want to track them in the Gemfile so we know we need to install them to the system.

Since resque uses redis we need to include that gem too. Redis is a key value storage mechanism that stores the data that's in the resque queue. Redis has a nice front end server we'll use later and it may be helpful to troubleshoot/watch the queue so let's install that too. 

```
sudo apt-get install redis-server
```

The [mail gem](https://github.com/plataformatec/mail_form) is used because it's good at handling mail strings. It makes things handy later when we are processing the email in rails.

Don't forget 'bundle install' to install these new gems.


#### Create a script to receive the email from postfix and add it to the resque worker queue

This script is what will be called from postfix later. You can put it in **lib/email_receiver**

```ruby
#!/usr/bin/env ruby
require 'rubygems'
require 'resque' 
require 'redis'
require 'mail'

class EmailReceive
  @queue = :incoming_email_queue

  def initialize(content)
    mail    = Mail.read_from_string(content)
    body    = mail.body.decoded
    from    = mail.from.first
    to      = mail.to.first 
    subject = mail.subject 


    if mail.multipart?
      part = mail.parts.select { |p| p.content_type =~ /text\/plain/ }.first rescue nil
      unless part.nil?
        message = part.body.decoded
      end
    else
      message = mail.decoded
    end 

    unless message.nil?
      Resque.enqueue(EmailReceive, from, to, subject, message)
    end
  end
end 

EmailReceive.new($stdin.read)
```


#### Create a Resque worker script to process the email queue
This script will be ran by Resque whenever it has stuff in its queue. You can put it in **lib/resque_process_email.rb**

```ruby
class InvalidReplyUser    < StandardError ; end

class EmailReceive
  @queue = :incoming_email_queue
  
  def self.perform(from, to, subject, body)
    user = User.find(2)
    if user.nil?
      raise InvalidReplyUser, "User with email = #{from} is not a member of the app."
    end 

    params = {
      :body     => body,
      :to       => to,
      :subject  => subject,
      :from     => from
    }

    message = user.messages.new(params)
    unless message.save
      raise RuntimeError, "Unable to save message. Errors: #{message.errors.inspect}"
    end
  end
end
```

That should be familiar ruby code at this point. The main points to know about resque is that there needs to be a class, a queue and the self.perform action with the same variables that were sent to it using the enqueue function in the previous script. Take note that in that example we are just putting all incoming mail into the user with id of 2. You can improve upon what user to find so the email is saved for the right person.

#### Start Resque
Create a file in your application named **lib/tasks/resque.rake** and add the following to it:

```ruby
require "resque/tasks"
task "resque:setup" => :environment
require "/home/user/rails/projectname/lib/resque_process_email.rb"
```

This will make sure the resque worker has access to the rails environment and knows what task is his.

Next start the resque job by doing:

```
rake resque:work QUEUE='*'
```

The '*' means to start all workers. You could specify the EmailReceive worker instead if you wish. This command needs to always be running in order for resque to process the queue.

It is important to note that any changes done to resque_process_email.rb will require the rake resque task to be restarted. 


#### Test functionality of the listening scripts

Take a quick sample email and put it into a file like sample.email. Something that looks like this:

```
From alice@yourdomain.com  Sun Apr  6 18:18:26 2014
Return-Path: <alice@yourdomain.com>
X-Original-To: bob@yourdomain.com
Delivered-To: bob@yourdomain.com
Received: by smtp.yourdomain.com (Postfix, from userid 1000)
        id A3D17282CF9; Sun,  6 Apr 2014 18:18:26 -0700 (PDT)
Subject: testing the ruby scripts
To: <bob@yourdomain.com>
X-Mailer: mail (GNU Mailutils 2.2)
Message-Id: <20140407011826.A3D17282CF9@smtp.yourdomain.com>
Date: Sun,  6 Apr 2014 18:18:26 -0700 (PDT)
From: alice@yourdomain.com (alice)

Hello, this is the body of the email.

```

If you put that into sample.email then you can run a command to test your ruby script by doing this:

```
cat ~/sample.email | ruby /path/to/projectname/lib/email_receiver_script
```

If all is right, the email should have been processed by the script and ended up in the database. 

If it makes it through your first script but not through the resque queue you have some cool tools to help you troubleshoot. Specifically there is a web gui to shows the resque worker queue. You can enable it by doing the following to your rails app.

**routes.rb**

```
mount Resque::Server, :at => "/resque"
```

You also need to make sure the Gemfile says `gem 'resque', :require => "resque/server"`. Once you do that and restart your rails app try going to http://hostname:3000/resque

It should be the gui to the Resque jobs. If it says something like cannot connect to redis then that means you don't have redis-server installed and need to go back and re-read how to install that earlier in this post.

From the resque gui you can dig down into any failed jobs and troubleshoot why something failed. It's very helpful and will tell you why something is broken.


### Create a RVM wrapper for your rails project
Ok here comes the tricky part. RVM? Wrapper? These are things I'll never understand but I'll try to explain what I know. RVM is the ruby version manager we used to install ruby and rails remember? [A wrapper](http://rvm.io/integration/init-d) is a way to create a ruby environment so that we can use that environment from other users/services (like postfix) to execute standalone ruby scripts.

Start by creating a new gemset:

```
rvm gemset create my_app
```

You should verify the location of your gemset. Doing `which ruby` may give you a clue to where it may be. For me it was at
/home/alice/.rvm/wrappers/ruby-2.1.1@my_app

Now go into your the directory of where your rails app lives and install the gems into that gemset using this command:

```
rvm 2.1.1@my_app do bundle install
```

Once that is done test that your script still works. This time switching 'ruby' with the new gemset version of ruby. Here is one way to test it if you still have the sample.email from earlier:

```
cat ~/sample.email | /home/alice/.rvm/wrappers/ruby-2.1.1@my_app/ruby /path/to/projectname/lib/email_receiver_script
```

Troubleshoot until the email is arriving into the database as expected.



## Setup Postfix

Whew, we've covered a lot and are on the home stretch now. Now we'll switch gears and get into postfix. Postfix is a MTA (mail transit authority). It listens on port 25 for incoming mail and accepts it and forwards it to where it needs to go. We could probably get away without needing an MTA if we were doing just outbound mail. But rails can't handle incoming mail very well so we need some kind of MTA to handle incoming mail. So let's first install postfix.

```
sudo apt-get install postfix postfix-mysql mailutils
```
Notice we are also installing the postfix-mysql package so that postfix can interface with our database.

After it installs configure it by doing the following command:

```
sudo dpkg-reconfigure postfix
```

You'll want to choose internet site, then the domain for your site you want to accept mail for. Also choose your domain as the final destination. You can say no to synchronous updates forced and choose the defaults for the rest of the options.

If the system you are on doesn't block outbound port 25 then you can test sending mail outbound with the following command:

```
date | mail -s test someone@some_external_domain.com
```

If you didn't get the email then check the logs to try to figure out what's wrong.

```
sudo tail -f /var/log/mail.log
```

#### Configure Virtual Mailboxes 

By default, postfix will only accept mail for users that have full accounts on that linux machine. We don't want this, we want to accept mail for any of the users we have in our rails app, or database. Edit the following two files.

**/etc/postfix/main.cf**

```
virtual_mailbox_domains = beebin.com
virtual_mailbox_base = /var/mail
virtual_mailbox_maps = mysql:/etc/postfix/mysql_mailbox_maps
virtual_uid_maps = static:155
virtual_gid_maps = static:1001
```

Create file: **/etc/postfix/mysql_mailbox_maps**

```
user=projectname
password=NEW_db_passw0rd
dbname=projectname_development
hosts=127.0.0.1
query = SELECT user_id FROM users WHERE email_address='%s'
```

Now we need to set some permissions for postfix.

```
sudo groupadd -g155 vmail
sudo useradd -c "Virtual Mail User" -g155 -u155 -s/bin/false vmail
```


Restart postfix by doing `sudo /etc/init.d/postfix restart`.

If you've created a user in your rails app and given them an email bob@yourdomain.com then we can test that mail is being accepted for them. Try sending a test mail to that address with this command:

```
date | mail -s test bob@yourdomain.com
```

Watch the `/var/log/mail.log` to see if the message was delivered. It should have the keyword 'delivered' in the logs with a 'removed' log also meaning the email is out of the queue of postfix.


#### Configure Postfix to deliver message to ruby
This is probably the hardest part of the whole process and you'll probably spend the most amount of time troubleshooting this. I hope I can explain it well for you.

Our last step is to add a header_check and filter 

**/etc/postfix/main.cf**

```
header_checks = regexp:/etc/postfix/header_checks
```

Create file: **/etc/postfix/header_checks**

```
/To:.*@yourdomain.com.*/ FILTER send_to_ruby_filter:
```

Add these two lines to the end of **/etc/postfix/master.cf**

```
send_to_ruby_filter unix -     n       n       -       -       pipe
 flags=Xhq user=alice argv=/home/alice/.rvm/wrappers/ruby-2.1.1@my_app/ruby /path/to/projectname/lib/email_receiver_script
```

Save it and restart postfix.
`sudo /etc/init.d/postfix restart`

What we've done here is tell postfix to check the headers of all emails and if the to: field has @yourdomain.com it must mean it's incoming mail so send it to the 'send_to_ruby_filter'. We define the 'send_to_ruby_filter' in master.cf and there we indicate a pipe command. This means output the mail message to whatever we indicate in the argv parameter. The flags Xhq means mark the mail as delivered, ignore case and preserver whitespace. See [man pipe](http://www.postfix.org/pipe.8.html) for more details about the flag meanings.


Now you can send a test email and see if it made it. Here's how to send a test email again:

```
date | mail -s test bob@yourdomain.com
```

Watch `/var/log/mail.log` for errors. Check your resque jobs to make sure it got processed. Look into the database to see if the data is there. If all is right it should be working!


So now if your system accepts incoming port 25 and dns is set up correctly you should be getting inbound emails from the internet into your rails app. Whew, what an adventure it's been!


### Additional links

Some other brave souls have tried to do this same thing. Here are their attempts.

[Dan Thoughts - Processing emails with Postfix and Rails](http://blog.sosedoff.com/2011/08/10/processing-emails-with-postfix-and-rails/)

[Jason Seifer - Receiving Email with Rails](http://jasonseifer.com/2009/04/24/receving-email-with-rails)

[The EdgeCase Library - Configuring Postfix to Deliver Mail to Ruby](http://library.edgecase.com/configuring-postfix-to-deliver-email-to-ruby)



