---
id: 13
title: 'The Second Rule of Programming: It’s sometimes not your fault'
date: 2019-09-02T18:55:22+01:00
author: waz
layout: post
fabulist-selected-sidebar:
  - sidebar-1
image: /wp-content/uploads/2019/09/apple-businesswoman-coding-7375-600x800.jpg
---
(with apologies to&nbsp;<a href="https://blog.codinghorror.com/the-first-rule-of-programming-its-always-your-fault/" target="_blank" rel="noreferrer noopener">Jeff Atwood</a>)

As you may know, one of my projects,&nbsp;<a rel="noreferrer noopener" href="https://play.google.com/store/apps/details?id=io.shoutapp.shout" target="_blank">Shout</a>, is primarily a location based messaging app. That means getting the user’s location is the highest priority.

We gather the location using the standard HTML5 Geolocation API, making a call to&nbsp;<a href="https://developer.mozilla.org/en-US/docs/Web/API/Geolocation/watchPosition" target="_blank" rel="noreferrer noopener">Geolocation.watchPosition()</a>. If the user agrees to share their location, it is pulled from various sources, primarily GPS.

On 13th March, we’d just pushed some code to production and&nbsp;<a rel="noreferrer noopener" href="https://engineering.shoutapp.io/u/a760000c91d9?source=post_page-----71d397f66adc----------------------" target="_blank">hughjd</a> began to notice something strange when using Shout on a desktop browser. Intermittently the user’s location wasn’t being identified.

However I was unable to reproduce the problem! So I then tried the app on Android, our primary platform, and here the issue was far more prevalent. In my testing, the location was never retrieved.

# A Confusing Problem {#06f5}

Being developers familiar with the&nbsp;<a href="https://blog.codinghorror.com/the-first-rule-of-programming-its-always-your-fault/" target="_blank" rel="noreferrer noopener">first rule of programming</a>, we assumed the problem would be found in our latest commits to master. We started digging through commits, but we couldn’t find anything that could cause this issue. In the call to watchPosition() you can pass an optional timeout parameter, which by default is infinite. When passing “{ timeout : 5000 }”, we actually got an error thrown instead of just ‘nothing’.

<div class="wp-block-image">
  <figure class="aligncenter"><img src="https://miro.medium.com/max/1306/1*nz1bvA4GiryKOTFB6OD7kQ.png" alt="" /></figure>
</div>

Even more confusingly, my desktop worked fine and Android didn’t work at all, whereas for Hugh, his Android worked (mostly) fine and desktop didn’t work! What is going on!?

<blockquote class="wp-block-quote">
  <p>
    Intermittent issues are the most annoying to debug
  </p>
</blockquote>

With no help to be found on Google or Stack Overflow, and nothing to go on except that we had both updated Chrome just before the issue appeared, we (skeptically!) decided to look at browser versions. Hugh was on 65.x while I was on 64.x. Surely this couldn’t be a Chrome issue? After changing a few chrome settings and still having it work for me, I finally updated my Chrome to 65.x…and boom, the issue appeared! So it is a Chrome bug!

# Confirming Our Suspicions {#f636}

With these latest findings, we decided to look outside of our app for confirmation that this issue was occuring elsewhere, so we decided to test browser location with&nbsp;<a href="https://browserleaks.com/geo" target="_blank" rel="noreferrer noopener">BrowserLeaks</a>. Upon opening the page, we got the “(3) TIMEOUT Timeout expired”, just as with our app with a timeout parameter! Some refreshes worked, then further ones returned the same timeout, and only closing and reopening Chrome did we get the location again.

This confirms it, a bug in Chrome

We also looked at other ways to retrieve the users location, although they turned out to be not as accurate.

# Awaiting And A Fix {#f7be}

So, if you were using Chrome 65.x+, then calls to location may have been causing issues on all platforms including mobile.

I went on to tweet at Google Devs, Chromium Devs and Android Devs, and it took a while but the bug was finally confirmed and fixed! If you want to read up on the exact problem, here&#8217;s the ticket for the issue &#8211; <https://bugs.chromium.org/p/chromium/issues/detail?id=820945>