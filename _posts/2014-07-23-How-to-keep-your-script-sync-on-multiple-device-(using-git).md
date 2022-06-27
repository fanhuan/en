---
layout: post
title:  How to keep your scripts sync on multiple device (using git)
categories: [notes]
tags: [Open lab note, Bioinformatic tools]
---
Usually I wrote python script on my local desktop and put it on a server for usage. Sometimes I begin to do stupid things such as edit the script on the server directly thus lose tract of things. I decided to be more organised in terms of my scripts by using git.

##1. Create a repo dedicated to my scripts on github website
##2. Clone it to my desktop.
	mydesktop$git clone https://github.com/*/script
##3. Move my scripts into the local script directory and push them online
	mydesktop$git clone add .
	mydesktop$git clone commit -am "version number"
	mydesktop$git clone push
##4. Clone it to my server
	myserver$git clone https://github.com/*/script
##5. Add the two directories to my path
	myserver$vi ~/.bashrc
	mydesktop$vi ~/.bashrc
Add export PATH=/path_to_git/script:$PATH to those two file.
And change the mode of all the scripts. $chmod a+x *.py
Now I do not need to worry where my scripts are (should be in the two script folders) and can excute them wherever. After working on the local file, push it online, and then update your repo on the server by
	
	myserver/path_script$git pull origin master
	
