---
id: 1335
title: Code A YouTube Subscriber Counter With Raspberry Pi
date: 2020-01-18T17:26:20+00:00
author: waz
layout: post
---
What better feature for the background in your new YouTube videos than a live updating subscriber counter? Well now you can build one for just a few dollars/pounds!

## Video Guide:

## Grab your hardware:

[MATRIX Display](https://www.amazon.co.uk/gp/product/B07HJDV3HN/ref=as_li_tl?ie=UTF8&camp=1634&creative=6738&creativeASIN=B07HJDV3HN&linkCode=as2&tag=wazcov09-21&linkId=2a992c027263ab01846234f35c788b69)

[Raspberry Pi 4](https://www.amazon.co.uk/gp/product/B07TC2BK1X/ref=as_li_tl?ie=UTF8&camp=1634&creative=6738&creativeASIN=B07TC2BK1X&linkCode=as2&tag=wazcov09-21&linkId=649310f6c7dd402b7563ec09b54bac99) or cheaper [Raspberry Pi 3](https://www.amazon.co.uk/gp/product/B07BDR5PDW/ref=as_li_tl?ie=UTF8&camp=1634&creative=6738&creativeASIN=B07BDR5PDW&linkCode=as2&tag=wazcov09-21&linkId=33f13995d4f62d45611ef803268b7268)

## Connect It Up:

<img loading="lazy" width="1024" height="768" src="https://devscover.com/wp-content/uploads/2020/01/pi_max72199.jpg" alt="" class="wp-image-1510" srcset="https://raw.githubusercontent.com/wazcov/wazcov.github.io/master/images/2020/01/pi_max72199.jpg 1024w, https://raw.githubusercontent.com/wazcov/wazcov.github.io/master/images/2020/01/pi_max72199-300x225.jpg 300w, https://raw.githubusercontent.com/wazcov/wazcov.github.io/master/images/2020/01/pi_max72199-768x576.jpg 768w, https://raw.githubusercontent.com/wazcov/wazcov.github.io/master/images/2020/01/pi_max72199-600x450.jpg" /> 

## Steps:

  * Connect to your Pi via SSH
  * Run&nbsp;`sudo raspi-config`&nbsp;, navigate to Interface Options, then enable SPI
  * Connect the Pi to the LED Matrix

  * Run the following commands: 

```
sudo apt-get update --fix-missing

sudo usermod -a -G spi,gpio pi

sudo apt install build-essential python3-dev python3-pip libfreetype6-dev libjpeg-dev libopenjp2-7 libtiff5

sudo apt-get install git

sudo -H pip3 install --upgrade --ignore-installed pip3 setuptools

```

  * Navigate to where you want to store the code
  * run `git clone git@github.com:wazcov/devscover-youtube.git`
  * (If you don&#8217;t have a git account, download the zip [here](https://github.com/wazcov/devscover-youtube)) 
  * run `cd devscover-youtube`
  * run `python3 examples/tick.py`
  * To alter to show your own channel, edit the text with your channel name inside the file using something like nano, then change the channel ID to be your own