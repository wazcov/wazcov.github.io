---
id: 9
title: My Adblock Headache Story
date: 2019-09-05T11:25:02+01:00
author: waz
layout: post
guid: http://devscover.com/?p=9
permalink: /?p=9
fabulist-selected-sidebar:
  - sidebar-1
image: /wp-content/uploads/2019/09/design-desk-eyewear-313690-600x800.jpg
---
A short post on a bug that really gave me a headache recently.

I’d started coding up a back end service using my go-to framework of Spring Boot, with a few basic controllers, repositories and services as per convention.

My choice for front end was React.JS due to it’s fast loading nature for data-intensive applications. The client requirement was also for a bootstrap front end, and there’s a fantastic framework called react-bootstrap that makes coding much faster. I also just like React.JS.<figure class="wp-block-image">

![](https://miro.medium.com/max/2560/0*G5d3L-GgKuw9CrVA.jpg) </figure> 

So I build a basic front end, and connect it up to some of the endpoints in the API. I test it locally and all looks peachy. So far so good. Now I whip up my favourite cloud computing service, AWS and deploy the UI and API on separate EC2 instances, but otherwise exactly as I’ve been running them locally.

I access the UI address but to my surprise, I just see the loading icon on the page, which I’d coded to appear while waiting for data to load from the API. This icon never disappears, so I check the developer console (Ctrl + Shift + i) and I see this message:

Blocked by client? Connection refused? Why would the client be blocking the API call…

So I try everything locally again, and it still works fine. I think, right maybe the ports are incorrectly set up on AWS, and the UI can’t communicate properly with the API. I spend a good amount of time changing ports, all to no avail. I query the API directly, and I get a nice pretty screen of JSON…so why can I retrieve data but not the UI?

After a period of time (which I’m not going to admit) spent digging through the code and server set-up I finally notice something on a blog page I’m reading. It’s asking me to disable adblock so I can continue reading the articles. I think…surely not….so I quickly navigate back to my AWS hosted UI and I see a little 1 icon next to adblock, telling me that it’s blocked an advert.

<div class="wp-block-image">
  <figure class="aligncenter"><img src="https://miro.medium.com/max/66/0*ffyDviWW_bPtFilA." alt="" /></figure>
</div>

I hit the button and tell it to disable ad blocking on this website, then reload to page. Boom, instant data.

Well that was embarrassing, I’d managed to use adblock to block my own legitimate data from being retrieved. And why? Well, the main endpoints for retrieving data were prefixed with&nbsp;_/adverts/_&nbsp;which adblock saw as actual ads. Now it’s time to go through the code and change every instance of Advert to Article or Post…