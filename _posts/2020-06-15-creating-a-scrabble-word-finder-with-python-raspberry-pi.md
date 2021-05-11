---
id: 3437
title: 'Creating A Scrabble Word Finder With Python &#038; Raspberry Pi'
date: 2020-06-15T16:36:30+01:00
author: waz
layout: post
image: /wp-content/uploads/2020/06/thumbnail-scaled.jpg
---
### So you want some help beating your friends or family at Scrabble? Well this may be the answer. {#intro}

Using just a bit of Python code, running on a Raspberry Pi with a HQ Camera Module, you can read your scrabble tiles, and have a display show you the top highest scoring words that you could play.<figure class="wp-block-image size-large">

<img loading="lazy" width="640" height="446" src="https://raw.githubusercontent.com/wazcov/wazcov.github.io/master/images/2020/06/IMG_2678.jpeg" alt="Raspberry Pi Touchscreen Showing Scrabble Words & Scores" class="wp-image-3435" srcset="https://raw.githubusercontent.com/wazcov/wazcov.github.io/master/images/2020/06/IMG_2678.jpeg 640w, https://raw.githubusercontent.com/wazcov/wazcov.github.io/master/images/2020/06/IMG_2678-300x209.jpeg 300w, https://raw.githubusercontent.com/wazcov/wazcov.github.io/master/images/2020/06/IMG_2678-600x418.jpeg 600w" sizes="(max-width: 640px) 100vw, 640px" /> <figcaption>Raspberry Pi Touchscreen Showing Scrabble Words & Scores</figcaption></figure> 

### Video Tutorial {#video-tutorial}

Here&#8217;s the video I made for my Devscover YouTube Channel:

<iframe width="560" height="315" src="https://www.youtube.com/embed/gcsv1fnDZzU" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

### Gathering Components {#gathering-components}

First you are going to want to gather all your components. Note the High Quality camera isn&#8217;t essential, you can use the older camera modules for Raspberry Pi. The advantage this new one gives you, is that it has a really good depth of field, so you can have your tiles in focus while the background is blurry.

### Connect It Up {#connect-it-up}

  1. Connect the display to the Raspberry Pi&#8217;s DSI Screen Port, and required jumper cables as per the included instructions
  2. Connect the HQ camera to the Raspberry Pi&#8217;s Camera port (this can look the same as the DSI one)
  3. [Optionally] Attach a tripod to the camera for easier mounting
  4. Get out your scrabble board and some tiles

### Install Dependencies {#install-dependencies}

You may or may not need to install the following dependencies, which allow Optical Character Recognition:

`sudo apt install libsm6 libxrender1 libfontconfig1 libatlas3-base libqtgui4 libqt4-test libwebp6 libtiff5 libjasper1 libilmbase12 libopenexr22 libilmbase12 libgstreamer1.0-0 libavcodec57 libavformat57 libavutil55 libswscale4 libqtgui4 libqt4-test libqtcore4`

You may also need to run a&nbsp;`sudo apt update`

### Install Pytesseract {#install-pytesseract}

This assumes you have Python 3 installed.

Installation is complicated, because the default way of installing should be

`sudo apt-get install tesseract-ocr`

however, that installs (at time of writing) version 4 of tesseract, which does not work with a whitelist.

Many attempts were made to use&nbsp;`--oem 0`&nbsp;with tesseract 4 to enable whitelisting, but then more errors sprang up about needing dictionaries.

Therefore the solution was to compile the library locally from the source using these commands:

  * git clone &#8211;depth 1&nbsp;&nbsp;<https://github.com/tesseract-ocr/tesseract.git>
  * cd tesseract-ocr/
  * ./autogen.sh
  * ./configure
  * make
  * sudo make install
  * sudo ldconfig
  * tesseract -v

Then you must download the english trained data from:&nbsp;<https://github.com/tesseract-ocr/tessdata/blob/master/eng.traineddata>

Next, set an environment variable to the location of that english trained data.

You can use&nbsp;`export TESSDATA_PREFIX=/home/pi/tesseract/tessdata`&nbsp;(replacing the location with the location of the folder you downloaded the traineddata into) in your CLI or add this to your&nbsp;`~/.bashrc`&nbsp;to ensure it is set on startup

### Install Python packages {#install-python-packages}

You also need to use pip3 to install required packages. Use this command

```
pip3 install opencv-python==3.4.6.27 pip3 install pytesseract pip3 install time pip3 install PySimpleGUI pip3 install Image
```

### Get the code {#get-the-code}

You can find the code here:&nbsp;<https://github.com/wazcov/devscover-youtube/tree/master/scrabble>

Git clone to download the&nbsp;repository to your&nbsp;Raspberry Pi

#### Modify the code

Next, you might want to alter a few bits to get it working for you&#8230;

Specifically this line&nbsp;&nbsp;that alters the point at which it decides what is black and what is white

<pre class="wp-block-preformatted">blackwhite = img.point(lambda x: 0 if x &lt; 166 else 255, '1')</pre>

the 166 worked very well for the light source I had. However, as the day progressed the light changed and it made pytesseract unable to read the tiles. This number is therefore very fickle &#8211; you may need to do a lot of tweaking to get it to read the image.

### Line up the image {#line-up-the-image}

To line up the image, I recommend using raspistill. This will preview for 15 seconds and save a picture to testImage.jpg

<pre class="wp-block-preformatted">raspistill -t 15000 -o testImage.jpg</pre>

### Run It! {#run-it-}

Simply run`python3 scrabbleWordFinder.py`

### How did it go? {#how-did-it-go-}

Let me know here or as a comment on my&nbsp;<a href="https://www.youtube.com/c/devscover?sub_confirmation=1" target="_blank" rel="noreferrer noopener">YouTube Channel</a>, and I&#8217;ll try and get back to you with tweaks and advice! Also, do subscribe on YouTube for plenty more cool tutorials like this!

You can also find me on my website&nbsp;<a href="https://devscover.com/" target="_blank" rel="noreferrer noopener">Devscover.com</a>