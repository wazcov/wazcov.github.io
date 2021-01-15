---
id: 171
title: How Install Raspbian On An SD For Raspberry Pi Using A Mac Terminal
date: 2019-09-17T07:34:17+01:00
author: waz
layout: post
image: /wp-content/uploads/2019/09/capacitors-chip-circuit-board-57007-scaled.jpg
---
Ever had trouble writing an .img file to an SD card for your Raspberry Pi? Here&#8217;s how to do it without installing any other programs.

## Download Your Image

Firstly, you need to download the latest image that you want to write. I personally like to use the full <a rel="noreferrer noopener" aria-label="Raspbian image (opens in a new tab)" href="https://www.raspberrypi.org/downloads/raspbian/" target="_blank">Raspbian image</a>. Download this to your Mac, somewhere memorable.

## Terminal Commands

To start the writing process, you need to find the path of your drive with:

<pre class="wp-block-code"><code lang="bash" class="language-bash line-numbers">diskutil list</code></pre><figure class="wp-block-image">

<img loading="lazy" width="1024" height="579" src="https://devscover.com/wp-content/uploads/2019/09/Screenshot-2019-09-16-at-20.31.06-1024x579.png" alt="" class="wp-image-172" srcset="http://localhost:8080/wp-content/uploads/2019/09/Screenshot-2019-09-16-at-20.31.06-1024x579.png 1024w, http://localhost:8080/wp-content/uploads/2019/09/Screenshot-2019-09-16-at-20.31.06-600x339.png 600w, http://localhost:8080/wp-content/uploads/2019/09/Screenshot-2019-09-16-at-20.31.06-300x170.png 300w, http://localhost:8080/wp-content/uploads/2019/09/Screenshot-2019-09-16-at-20.31.06-768x434.png 768w, http://localhost:8080/wp-content/uploads/2019/09/Screenshot-2019-09-16-at-20.31.06.png 1546w" sizes="(max-width: 1024px) 100vw, 1024px" /> </figure> 

You can identify your SD card by the fact it says &#8216;external&#8217; and the size, in my case, is 8.0GB.

Now you need to use that path (mine is /dev/disk2) for the next command, which will **wipe your disk**:

<pre class="wp-block-code"><code lang="bash" class="language-bash">diskutil eraseDisk ExFAT disk2 /dev/disk2</code></pre><figure class="wp-block-image">

<img loading="lazy" width="1024" height="621" src="https://devscover.com/wp-content/uploads/2019/09/Screenshot-2019-09-16-at-20.32.44-1024x621.png" alt="" class="wp-image-173" srcset="http://localhost:8080/wp-content/uploads/2019/09/Screenshot-2019-09-16-at-20.32.44-1024x621.png 1024w, http://localhost:8080/wp-content/uploads/2019/09/Screenshot-2019-09-16-at-20.32.44-600x364.png 600w, http://localhost:8080/wp-content/uploads/2019/09/Screenshot-2019-09-16-at-20.32.44-300x182.png 300w, http://localhost:8080/wp-content/uploads/2019/09/Screenshot-2019-09-16-at-20.32.44-768x466.png 768w, http://localhost:8080/wp-content/uploads/2019/09/Screenshot-2019-09-16-at-20.32.44.png 1516w" sizes="(max-width: 1024px) 100vw, 1024px" /> </figure> 

Once you&#8217;ve seen that the disk has been erased, you can move onto writing your .img file to your SD. 

Here you need to know both the path of the .img you downloaded and the drive again:

<pre class="wp-block-code"><code lang="bash" class="language-bash line-numbers">sudo dd if=/Users/waynecovell/Desktop/2019-07-10-raspbian-buster-full.img of=/dev/disk2 bs=1m</code></pre>

It may look like nothing is happening at this point &#8211; and it will take a long time. To check progress, just hit **_Ctrl+T_** to see progress like this:<figure class="wp-block-image">

<img loading="lazy" width="1024" height="133" src="https://devscover.com/wp-content/uploads/2019/09/Screenshot-2019-09-16-at-20.50.29-1024x133.png" alt="" class="wp-image-174" srcset="http://localhost:8080/wp-content/uploads/2019/09/Screenshot-2019-09-16-at-20.50.29-1024x133.png 1024w, http://localhost:8080/wp-content/uploads/2019/09/Screenshot-2019-09-16-at-20.50.29-600x78.png 600w, http://localhost:8080/wp-content/uploads/2019/09/Screenshot-2019-09-16-at-20.50.29-300x39.png 300w, http://localhost:8080/wp-content/uploads/2019/09/Screenshot-2019-09-16-at-20.50.29-768x100.png 768w, http://localhost:8080/wp-content/uploads/2019/09/Screenshot-2019-09-16-at-20.50.29.png 1336w" sizes="(max-width: 1024px) 100vw, 1024px" /> </figure> 

Boom! Done! Easy. Just pop the SD card into your Raspberry Pi and get going.

## Resource Busy?

If you happen to encounter &#8216;**_resource busy_**&#8216;, like I did, you can run this command after formatting your SD, to unmount it:

<pre class="wp-block-code"><code lang="bash" class="language-bash">unmount /dev/disk2</code></pre>