---
id: 2553
title: Automate Your Project Setup With Python
date: 2020-03-19T21:16:45+00:00
author: waz
layout: post
---
First you need to alter your startup script. On Linux and older Mac OSs this is ~/.bashrc, however on Mac OS Catalina this may be ~/.zshrc.

Open the aformentioned file with a text editor and add this line:

```
source ~/.mycommands.sh
```

Then create a file in your home directory ~/.mycommands.sh with this inside:

```
function build() {
	./buildProject.py $1
	cd /Users/joebloggs/SoftwareDev/
	cd $1
	git init
	sleep 10
	git add -A
	git commit -m "Init Commit"
        git remote add origin git@github.com:jbloggs/$1.git
        git push -u origin master
        open -na "IntelliJ IDEA.app" /Users/joebloggs/SoftwareDev/$1
	#linux - /opt/idea/bin/idea.sh
}
```

Of course replace the path with your own path that you want to store your projects, and **jbloggs** with your Github username

Then finally you will need to create your python script in ~/.buildProject.py

```
#! /usr/bin/python3
import sys
import os
import time
from github import Github

project_name = sys.argv[1]

path = '/Users/joebloggs/SoftwareDev/'+project_name
print(path)
try:
    os.mkdir(path)
    print("Created Path")
except FileExistsError:
    print("Path Exists")

fileloc = path+'/README.md'
file = open(fileloc, 'w+')
file.write('Project: '+project_name+'\n')
file.close()

g = Github("XXX")
user = g.get_user()
repo = user.create_repo(project_name)
```

You can get your GitHub access token from Github settings <https://github.com/settings/tokens> and replace it in the script where there is XXX

You can also view a video of this tutorial here<figure class="wp-block-embed-youtube wp-block-embed is-type-video is-provider-youtube wp-embed-aspect-16-9 wp-has-aspect-ratio">

<iframe width="560" height="315" src="https://www.youtube.com/embed/BS5kRwW1FLI" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>