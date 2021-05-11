---
id: 11
title: Forcing reload of an image in HTML
date: 2019-09-03T07:58:30+01:00
author: waz
layout: post
fabulist-selected-sidebar:
  - sidebar-1
image: /wp-content/uploads/2019/09/art-background-blocks-1093914-600x800.jpg
---
# Browser Cache {#7225}

Browsers were designed with 14k modems in mind, and therefore cache almost anything they can find on a web page. In principle this is a good idea, it prevents unnecessary web requests to servers. However, in 2019 we don’t really care about that as much. Data speeds have got to the point where we are happy streaming a video from our mobile, let alone our desktop.

# The Problem {#8aa5}

This caching also causes some issues, specifically with things like CSS image scrollers or animated SVGs. My particular problem was that an animated SVG (generated with&nbsp;<a href="https://maxwellito.github.io/vivus-instant/" target="_blank" rel="noreferrer noopener">Vivius Instant</a>) would animate on first load, but for each subsequent load, it would simply display fully animated. I could have put the SVG on a loop, but I didn’t want it constantly appearing and disappearing while a user was on a page.<figure class="wp-block-image">

![](https://miro.medium.com/max/1880/0*O_dx_n7_uN6DYACp.) </figure> 

# The Hack {#e4a8}

So the trick is to make the browser think that the image you’re trying to load is a different image each time. To do this we need to change the source.

So where before we had a simple img tag:

```&lt;img style=”width:400px;min-height:130px;” src=”imgs/logo_animated.svg” alt=”fullPage” />```

We now remove the src of the image, and give it an ID:

```
&lt;img style=”width:400px;min-height:130px;” id=”mainLogo” alt=”fullPage” />
```

Then, we add a simple jQuery script to the head (you could also do this in plain JS):

```
&lt;script>
 $(document).ready(function() {
 var url = “imgs/logo_animated.svg?rnd=”+Math.random();
 $(‘#mainLogo’).attr(“src”,url);
 });
 &lt;/script>
 ```

This script will assign a random number to the end of the image request, which actually does nothing at all, except make the browser think that

```
logo_animated.svg?rnd=5234
```

is a different image to

```
logo_animated.svg?rnd=8374
```

# It’s Not Perfect {#31f6}

It isn’t a perfect solution. If the user has JS disabled, then you won’t see any image at all. However in my experience this is such a tiny edge case, because in 2019, that user will find that almost every website won’t work.

Also, there is a chance that the random number generator will produce an identical number, although given the real generator uses many decimal places, this is also a very slim edge case.

Let me know if this helped you!