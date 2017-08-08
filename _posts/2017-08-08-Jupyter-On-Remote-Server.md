---
layout: post
title:  How to set up Jupyter with Python 3.6 on a remote sever (Ubuntu 16.04)
categories: [notes]
tags: [python]
---

I went to SDSC Summer Institute last week and had an amazing time. Check out the [agenda](http://si17.sdsc.edu/agenda/). There were a few life-changing moments, which I think I will talk about in later posts. During the workshop, we used jupyter notebook that was installed on Comet, one of the super computers in SDSC, which allowed usage of multicores across the cluster. Therefore the first thing I wanted to do when I was back was to setup jupyter notebook on the work station in the lab.  


It's a pretty powerful work station: 40 cores, 330G of RAM (with 3 empty slots that we could still fill), and a 60T storage system mounted on it. As I do most of my computing on it, I usually write python scripts to run there, instead of using the more interactive jupyter notebook. I only use jupyter notebook on my iMac for exploratory analysis in early stages of data processing, therefore lots of file transferring happens between my iMac and the work station, which is not ideal. Not to mention the differences in computing power.

Here is a very helpful [post](https://www.digitalocean.com/community/tutorials/how-to-set-up-a-jupyter-notebook-to-run-ipython-on-ubuntu-16-04) if you want to set up a Jupyter Notebook on Ubuntu 16.04 with __Python 2.7__. However since it is already 2017 and Andrea Zonca, the organizer of the Summer Institute, doesn't want to hear about Python 2 any more, also I would like to move from multiprocessing to concurrent.futures, which is a new feather in Python 3.6, I decided to write down what I did to set up Jupyter Notebook for __Python 3.6__, still on Ubuntu 16.06.   

## Step 1: install jupyter notebook

There are many ways you could do it. Here I recommend Anaconda to save you from some hassles (very likely [sqlite3 error](https://stackoverflow.com/questions/42766063/python3-6-import-sqlite3-error)). This is a very good [post](https://www.digitalocean.com/community/tutorials/how-to-install-the-anaconda-python-distribution-on-ubuntu-16-04) for installing Anaconda on a remote server __for yourself__, however, imaging myself talking everyone in the lab into using jupyter notebook from the server, I decided to install it for all users. Here is very helpful [post](https://stackoverflow.com/questions/27263620/how-to-install-anaconda-python-for-all-users) for it. As you can see, I did not contribute to this topic at all except putting other people's posts together and some ground-truthing.

#### 1. Create a directory that's accessible for all users to install Anaconda.

		$sudo mkdir /opt/anaconda 
		$sudo chmod ugo+w /opt/anaconda  

#### 2. Check [here](https://repo.continuum.io/archive/) to see which one you want to download and install
		$cd /tmp
		$curl -O https://repo.continuum.io/archive/Anaconda3-4.4.0-Linux-x86_64.sh
		$bash Anaconda3-4.4.0-Linux-x86_64.sh
		
When you are asked __"Anaconda3 will now be installed into this location:"__, enter `/opt/anaconda`, the one we created in step 1.  
When you are asked __"Do you wish the installer to prepend the Anaconda3 install location"__, say __yes__ so the jupyter you use later is the one in Anaconda in case you have other copies elsewhere. `export PATH="/opt/anaconda/anaconda3/bin:$PATH"` is now in your .bashrc but you won't have it in effect until you restart your shell.  

## Step 2: Access from your local browser 
This is pretty much repeating this [post](https://www.digitalocean.com/community/tutorials/how-to-set-up-a-jupyter-notebook-to-run-ipython-on-ubuntu-16-04).  

__Currie lab users__: `export PATH="/opt/anaconda/anaconda3/bin:$PATH"` if you haven't done so. Write it to your .bashrc if you haven't dones so.

#### 1. SSH Tunneling  
This opens a port (in this example 8000) on your remote server for your local browser to access. On your __local computer__:
  
		$ ssh -L 8000:localhost:8888 your_server_username@your_server_ip
		
#### 2. Start jupyter on the remote server

		$ jupyter notebook  

It asked me whether I "Accepting one-time-token-authenticated connection from 127.0.0.1". I answered '__A__laways' but next time it kept asking me... Then it complains:

		Jupyter Notebook requires JavaScript.
   		Please enable it to proceed.  

Just ingore it buy entering Q. Then your token would be given on the last line, some thing like: `http://localhost:8888/?token=5640c991ffc0c0c6071e9f0d0100d7204e4b05a6d400c440`

#### 3. Access from your local browser  
__Replace 8888 with 8000__, since the later is the port we opened for your local machine, so go to `http://localhost:8000/?token=5640c991ffc0c0c6071e9f0d0100d7204e4b05a6d400c440` in your local browser and you are ready to go!

You can also create a password if you don't like to use token everytime. 

Don't forget to ask your users to add opt/anaconda/anaconda3/bin to their PATH.

I hope this works for you!


