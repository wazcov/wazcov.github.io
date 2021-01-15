---
id: 216
title: Extend Your EC2 Storage Volume Size
date: 2019-12-06T11:49:19+00:00
author: waz
layout: post
image: /wp-content/uploads/2019/12/Screenshot-2019-12-06-at-11.30.21.png
---
So you&#8217;ve created an EC2 but not given it enough storage. Now you&#8217;ve run out and are wondering how you change it.<figure class="wp-block-image size-large is-resized">

<img loading="lazy" src="https://devscover.com/wp-content/uploads/2019/12/Screenshot-2019-12-04-at-12.01.03-1024x235.png" alt="EC2 With 8GB Storage" class="wp-image-218" width="580" height="133" srcset="http://localhost:8080/wp-content/uploads/2019/12/Screenshot-2019-12-04-at-12.01.03-1024x235.png 1024w, http://localhost:8080/wp-content/uploads/2019/12/Screenshot-2019-12-04-at-12.01.03-scaled-600x138.png 600w, http://localhost:8080/wp-content/uploads/2019/12/Screenshot-2019-12-04-at-12.01.03-300x69.png 300w, http://localhost:8080/wp-content/uploads/2019/12/Screenshot-2019-12-04-at-12.01.03-768x176.png 768w, http://localhost:8080/wp-content/uploads/2019/12/Screenshot-2019-12-04-at-12.01.03-1536x352.png 1536w, http://localhost:8080/wp-content/uploads/2019/12/Screenshot-2019-12-04-at-12.01.03-2048x470.png 2048w" sizes="(max-width: 580px) 100vw, 580px" /> <figcaption>An EC2 With 8GB Storage</figcaption></figure> 

Here&#8217;s how you do it.

First SSH into your EC2 (Make sure your security groups are set that you can access port 22, you have the SSH Keys and replace the IP with that of your own EC2)

<pre class="wp-block-code"><code class="">ssh ec2-user@34.243.248.159</code></pre>

Next run this command to show your available space

<pre class="wp-block-code"><code class="">lsblk</code></pre><figure class="wp-block-image size-large">

<img loading="lazy" width="808" height="284" src="https://devscover.com/wp-content/uploads/2019/12/Screenshot-2019-12-06-at-11.28.22.png" alt="" class="wp-image-237" srcset="http://localhost:8080/wp-content/uploads/2019/12/Screenshot-2019-12-06-at-11.28.22.png 808w, http://localhost:8080/wp-content/uploads/2019/12/Screenshot-2019-12-06-at-11.28.22-600x211.png 600w, http://localhost:8080/wp-content/uploads/2019/12/Screenshot-2019-12-06-at-11.28.22-300x105.png 300w, http://localhost:8080/wp-content/uploads/2019/12/Screenshot-2019-12-06-at-11.28.22-768x270.png 768w" sizes="(max-width: 808px) 100vw, 808px" /> <figcaption>lsblk command showing 8gb</figcaption></figure> 

Here you can see that your root block device, and it&#8217;s only partition are of 8GB

To change this, go to your EC2 Instance in the AWS Console and look for the root device. Hover over this /dev/xvda link:<figure class="wp-block-image size-large">

<img loading="lazy" width="1024" height="606" src="https://devscover.com/wp-content/uploads/2019/12/Screenshot-2019-12-06-at-11.31.23-1024x606.png" alt="AWS ec2 management page" class="wp-image-239" srcset="http://localhost:8080/wp-content/uploads/2019/12/Screenshot-2019-12-06-at-11.31.23-1024x606.png 1024w, http://localhost:8080/wp-content/uploads/2019/12/Screenshot-2019-12-06-at-11.31.23-600x355.png 600w, http://localhost:8080/wp-content/uploads/2019/12/Screenshot-2019-12-06-at-11.31.23-300x178.png 300w, http://localhost:8080/wp-content/uploads/2019/12/Screenshot-2019-12-06-at-11.31.23-768x455.png 768w, http://localhost:8080/wp-content/uploads/2019/12/Screenshot-2019-12-06-at-11.31.23-1536x909.png 1536w, http://localhost:8080/wp-content/uploads/2019/12/Screenshot-2019-12-06-at-11.31.23.png 1818w" sizes="(max-width: 1024px) 100vw, 1024px" /> <figcaption>Hover over /dev/xvda</figcaption></figure> 

Next click the link which should be the EBS ID, to be taken to the volumes page.

From here you can click Actions > Modify Volume<figure class="wp-block-image size-large">

<img loading="lazy" width="1024" height="965" src="https://devscover.com/wp-content/uploads/2019/12/Screenshot-2019-12-06-at-11.32.53-1024x965.png" alt="" class="wp-image-240" srcset="http://localhost:8080/wp-content/uploads/2019/12/Screenshot-2019-12-06-at-11.32.53-1024x965.png 1024w, http://localhost:8080/wp-content/uploads/2019/12/Screenshot-2019-12-06-at-11.32.53-600x565.png 600w, http://localhost:8080/wp-content/uploads/2019/12/Screenshot-2019-12-06-at-11.32.53-300x283.png 300w, http://localhost:8080/wp-content/uploads/2019/12/Screenshot-2019-12-06-at-11.32.53-768x724.png 768w, http://localhost:8080/wp-content/uploads/2019/12/Screenshot-2019-12-06-at-11.32.53.png 1348w" sizes="(max-width: 1024px) 100vw, 1024px" /> <figcaption>Volumes Page</figcaption></figure> 

You&#8217;ll then have a window where you can change the 8GB to whatever you want. Let&#8217;s use 100GB, and press modify.

This may take some time to take effect, so go grab a cuppa.

After a couple of minutes, you should be able to view the storage on your EC2 again using the same command, and this time see the 100GB:<figure class="wp-block-image size-large">

<img loading="lazy" width="828" height="456" src="https://devscover.com/wp-content/uploads/2019/12/Screenshot-2019-12-06-at-11.34.48.png" alt="lsblk two" class="wp-image-241" srcset="http://localhost:8080/wp-content/uploads/2019/12/Screenshot-2019-12-06-at-11.34.48.png 828w, http://localhost:8080/wp-content/uploads/2019/12/Screenshot-2019-12-06-at-11.34.48-600x330.png 600w, http://localhost:8080/wp-content/uploads/2019/12/Screenshot-2019-12-06-at-11.34.48-300x165.png 300w, http://localhost:8080/wp-content/uploads/2019/12/Screenshot-2019-12-06-at-11.34.48-768x423.png 768w" sizes="(max-width: 828px) 100vw, 828px" /> <figcaption>Now showing 100GB</figcaption></figure> 

This is great, but we&#8217;re not done yet, since the root volume is 100GB but our default partition is still 8GB, so we need to expand it.

Assuming your root volume is named the same as mine, you&#8217;ll issue this growpart command to tell it to expand it&#8217;s first partition:

<pre class="wp-block-code"><code class=""> sudo growpart /dev/xvda 1</code></pre>

Another lsblk command will this time show your expanded partion<figure class="wp-block-image size-large">

<img loading="lazy" width="976" height="222" src="https://devscover.com/wp-content/uploads/2019/12/Screenshot-2019-12-06-at-11.38.30.png" alt="lsblk" class="wp-image-242" srcset="http://localhost:8080/wp-content/uploads/2019/12/Screenshot-2019-12-06-at-11.38.30.png 976w, http://localhost:8080/wp-content/uploads/2019/12/Screenshot-2019-12-06-at-11.38.30-600x136.png 600w, http://localhost:8080/wp-content/uploads/2019/12/Screenshot-2019-12-06-at-11.38.30-300x68.png 300w, http://localhost:8080/wp-content/uploads/2019/12/Screenshot-2019-12-06-at-11.38.30-768x175.png 768w" sizes="(max-width: 976px) 100vw, 976px" /> </figure> 

Now **WAIT**! You may think you&#8217;ve finished and can happily go ahead and use your 100GB&#8230;but that&#8217;s not the case.

You&#8217;ve created a bigger volume, extended your partition but your file system still doesn&#8217;t know about this extra size and will be 8GB. Don&#8217;t believe me? Run this command:

<pre class="wp-block-code"><code class="">df -hT</code></pre><figure class="wp-block-image size-large">

<img loading="lazy" width="1024" height="310" src="https://devscover.com/wp-content/uploads/2019/12/Screenshot-2019-12-06-at-11.42.34-1024x310.png" alt="df -ht 8gb" class="wp-image-243" srcset="http://localhost:8080/wp-content/uploads/2019/12/Screenshot-2019-12-06-at-11.42.34-1024x310.png 1024w, http://localhost:8080/wp-content/uploads/2019/12/Screenshot-2019-12-06-at-11.42.34-600x181.png 600w, http://localhost:8080/wp-content/uploads/2019/12/Screenshot-2019-12-06-at-11.42.34-300x91.png 300w, http://localhost:8080/wp-content/uploads/2019/12/Screenshot-2019-12-06-at-11.42.34-768x232.png 768w, http://localhost:8080/wp-content/uploads/2019/12/Screenshot-2019-12-06-at-11.42.34.png 1224w" sizes="(max-width: 1024px) 100vw, 1024px" /> </figure> 

See, the /dev/xvda1 file system is still 8GB. The way to extend this depends on the type. If, like here, yours is XFS, run this command:

<pre class="wp-block-code"><code class="">sudo xfs_growfs /dev/xvda1</code></pre>

or if your system shows ext, run:

<pre class="wp-block-code"><code class="">sudo resize2fs /dev/xvda1</code></pre>

Now perform your df command again:

<pre class="wp-block-code"><code class="">df -hT</code></pre><figure class="wp-block-image size-large">

<img loading="lazy" width="1024" height="279" src="https://devscover.com/wp-content/uploads/2019/12/Screenshot-2019-12-06-at-11.44.57-1024x279.png" alt="df -hT success" class="wp-image-244" srcset="http://localhost:8080/wp-content/uploads/2019/12/Screenshot-2019-12-06-at-11.44.57-1024x279.png 1024w, http://localhost:8080/wp-content/uploads/2019/12/Screenshot-2019-12-06-at-11.44.57-600x164.png 600w, http://localhost:8080/wp-content/uploads/2019/12/Screenshot-2019-12-06-at-11.44.57-300x82.png 300w, http://localhost:8080/wp-content/uploads/2019/12/Screenshot-2019-12-06-at-11.44.57-768x209.png 768w, http://localhost:8080/wp-content/uploads/2019/12/Screenshot-2019-12-06-at-11.44.57.png 1276w" sizes="(max-width: 1024px) 100vw, 1024px" /> <figcaption>Boom, 100GB.</figcaption></figure> 

Now you can see 100GB and you&#8217;re good to go and use this space for all your cat memes.

<div class="wp-block-image">
  <figure class="aligncenter"><img src="https://media.giphy.com/media/3oKIPnAiaMCws8nOsE/giphy.gif" alt="Image result for cat coding" /></figure>
</div>