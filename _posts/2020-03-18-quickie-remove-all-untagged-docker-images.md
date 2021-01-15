---
id: 2554
title: 'Quickie: Remove All Untagged Docker Images'
date: 2020-03-18T12:57:21+00:00
author: waz
layout: post
guid: https://devscover.com/?p=2554
permalink: /?p=2554
image: /wp-content/uploads/2020/03/dockertaghero.jpg
---
Here&#8217;s a quick one regarding deleting all untagged docker images. This occurred when `docker image prune` wasn&#8217;t deleting all the unused images I had. 

A bit of googling found this **wrong** command:

<pre class="wp-block-code"><code class="">docker rmi $(docker images | grep "^&lt;none>" | awk "{print $3}")</code></pre>

Amazingly, this command was on a bunch of Stack Overflow posts and various blogs, yet for me the command just didn&#8217;t work. Firstly, the sub command wasn&#8217;t listing all the images and secondly the AWK command wasn&#8217;t working. 

So with just a bit of tweaking, I got this

<pre class="wp-block-code"><code class="">docker rmi $(docker images -a | grep "^&lt;none>" | awk '{print $3}')</code></pre>

It might only be a couple of minor changes, but it made the difference! You can also swap the `"^<none>"` for any other string like `"oracle"` to filter different images.

Hopefully this helped you out! And you can see this working below:

<pre class="wp-block-code"><code class="">[ec2-user@ip-10-0-183-23 ~]$ docker rmi -f $(docker images -a | grep "^&lt;none>" | awk '{print $3}') 
Deleted: sha256:819f5fb82e4e84ef9f6a4c499794e7ff633142bc41da3d242b84012aee4a6107
Deleted: sha256:7d31f64668021acfde80ad52ff17ef129bac6f4ba0e102af71ed9e2faa7218bb
Deleted: sha256:9bbd7b6beb77f145f28b8e778fcd168ff0142a581d83871d22bdb339edce8e16
Deleted: sha256:5e65d9f730f3ea97e58266610e186e3423fa8dbcf43183d0763c6a9fc8cfefeb
Deleted: sha256:55c1eca785c7cd660f42749c623f4e60cc6d32e52397cda01ce5d4eab8d13131
Deleted: sha256:f4d619b209b5a07a6a365554233613ebd75e3002f8c2ea3198828674238cef24
Deleted: sha256:5ba856305748d36c328d01346f383b3bc7ea97abc439ae5707f75789f2ba7037
Deleted: sha256:e216d97404d66f06b58d0ac8722cf3e3674ec76b58c74d429167d00bfb9c3304
Deleted: sha256:98eafcd4f66683380c2fed6849a8d16749e6df0b7d6634eb5fd134ad3f483d4d
....etc</code></pre>