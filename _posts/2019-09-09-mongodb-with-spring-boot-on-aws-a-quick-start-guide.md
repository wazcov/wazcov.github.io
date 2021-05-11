---
id: 111
title: 'MongoDB with Spring Boot on AWS: A quick-start guide*'
date: 2019-09-09T07:40:38+01:00
author: waz
layout: post
fabulist-selected-sidebar:
  - sidebar-1
image: /wp-content/uploads/2019/09/aws-mongo-spring.png
---
<p class="has-small-font-size">
  <em>*Assuming basic knowledge of AWS and Java</em>
</p>

While working on**&nbsp;**<a rel="noreferrer noopener" href="https://itunes.apple.com/us/app/shout-app/id1389105586" target="_blank"><strong>Shout</strong></a>**,**&nbsp;I went looking for a quickstart guide to installing MongoDB on AWS, but all I found was a 27 page document from Amazon talking about private clouds, elastic blocks and other madness I’ve never used; so I went it alone and found it surprisingly straightforward! So here’s my real quickstart guide<figure class="wp-block-image">

![](https://miro.medium.com/max/1600/1*Qibh2TcNSKcFFsWui-MP5Q.png) </figure> 

## Provision an AWS Server {#e0c0}

I used Amazon Linux (Essentially CentOS) for this, just because I’m used to it, but a lot of people will use Ubuntu. The specs don’t really matter too much, but do&nbsp;_make sure you provision at least enough storage space for your documents._ Remember also to save the SSH keys that gets generated as part of the provision wizard.

## Initially open up ports {#7777}

To get started, it’s easiest if you open up ports for SSH (22) and Mongo ( 27017) to anywhere. Don’t worry, we’ll lock it down later. Use Security Groups under the EC2 settings page to do this.

## SSH Into Server {#52a8}

If you’re on Windows, install Putty. If your on a Linux PC or a Mac, you can just use a terminal. You should use the authentication keys provided by AWS when you provisioned the server (you may need to convert them into an appropriate format using PuttyGEN).

## Install Mongo {#1baa}

First you want to create a file so you can install mongo with yum. Run this:

```
sudo vim /etc/yum.repos.d/mongodb-org-3.6.rep
```

and in it, you need this:

```
[mongodb-org-3.6]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/amazon/2013.03/mongodb-org/3.6/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.6.asc
```

You can now easily run yum to install mongo:

```sudo yum install -y mongodb-org```

You can then start mongo with this command:

```sudo service mongod start```

You can confirm it’s running by checking the logs:

```sudo cat /var/log/mongodb/mongod.log```

You’re looking for a line like

```
[initandlisten] waiting for connections on port &lt;port>
```

and I’d really recommend making sure mongo starts up when the server starts (incase of a power outage or a restart)

```
sudo chkconfig mongod on
```

## Open a mongo session on the command line {#d94e}

```
mongo
```

_(Who’d have guessed!)_

## Create a database {#5f24}

```
use mycustom_db
```

## Create a user {#4b7c}

```
db.createUser({
    user: 'steve',
    pwd: 'AsecretPassword',
    roles: [{ role: 'readWrite', db:'mycustom_db'}]
})
```

Hopefully I don’t need to tell you to not actually use&nbsp;`'AsecretPassword'`&nbsp;as your password…

## Open Mongo Access {#4454}

<pre class="wp-block-code"><code lang="bash" class="language-bash line-numbers">sudo vim /etc/mongod.conf</code></pre>

Then edit the bindIp like so:

```
# network interfaces
net:
  port: 27017
# bindIp: 127.0.0.1  &lt;- change this line to:
  bindIp: 0.0.0.0
```

You also want to scroll down to security and make sure it looks like this, so it’s enabled:

```
security:
  authorization: 'enabled'
```

## Test Locally {#7c08}

This step is going to be specific to your Spring Boot application, but in most cases, if you already have a local mongo db working, it should be a case of changing either the application.properties file or the application.yml file, to something like this:

```
spring:
  data:
    mongodb:
      uri: mongodb://steve:AsecretPassword@ec2-XX-XXX-XXX-XXX.us-west-2.compute.amazonaws.com/mycustom_db
```

You can find your EC2 public DNS address in AWS easily in a number of places:<figure class="wp-block-image">

<img loading="lazy" width="1024" height="193" src="https://devscover.com/wp-content/uploads/2019/09/aws-1024x193.png" alt="" class="wp-image-132" srcset="https://raw.githubusercontent.com/wazcov/wazcov.github.io/master/images/2019/09/aws-1024x193.png 1024w, https://raw.githubusercontent.com/wazcov/wazcov.github.io/master/images/2019/09/aws-600x113.png 600w, https://raw.githubusercontent.com/wazcov/wazcov.github.io/master/images/2019/09/aws-300x57.png 300w, https://raw.githubusercontent.com/wazcov/wazcov.github.io/master/images/2019/09/aws-768x145.png 768w, https://raw.githubusercontent.com/wazcov/wazcov.github.io/master/images/2019/09/aws.png 1444w" sizes="(max-width: 1024px) 100vw, 1024px" /> </figure> 

Run your app, and check you get results.

I personally installed MongoDB Compass, a GUI that allows you to easily connect to your instance and check that results are showing.

## Update your security group {#6fdf}

Once you’ve confirmed it’s all working locally, you want to push your changes to your app to the server it runs on, then configure the security groups to only allow access to that mongo server from your web application server…

As you can see, we’ve removed SSH, as we don’t need it anymore and we’ve restricted the inbound rules to a security group.<figure class="wp-block-image">

![](https://miro.medium.com/max/2284/1*HLT7S1TS_8h0qf6iT4F-oQ.png) </figure> 

If you try and connect to this locally, you should get spring failing (that’s a good thing)

However, if you access your web app in a browser, it should connect fine and bring back results.

Done! How easy was that!?&nbsp;**Let me know in the comments below**&nbsp;if this helped, or if you have any questions!