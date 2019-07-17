---
layout: post
title:  The IT Crowd
categories: [notes]
tags: [IT][Linux]
---

	"Oh you are a data scientist! Can you fix my computer?"
	"No."
	
	
Today I was asked to install a linux system on a laptop with windows on. This is not the type of work I'd like to take at all, but I need to go to a long meeting so I thought I could do this at the background since most of the time you just wait. But when I asked what type of linux this person wants (I assumed Ubuntu), I was told CentOS. I've never installed it before so here is to remind me not to take dirty jobs like this.

## Step 1 : making a USB stick as your installer

Found this helpful [post](https://wiki.centos.org/HowTos/InstallFromUSBkey) on setting up a USB key to install CentOS. The only problem is that I have Mac. Therefore needed this [post](https://www.cyberciti.biz/faq/how-to-create-disk-image-on-mac-os-x-with-dd-command/) to help with the use of `dd`. In short:

1. Download a centOS iso
2. Plug in your USB and find out where it is mounted by `duskutil list`. In my case it is mounted at `/dev/disk2`. 
3. Unmount the disk `diskutil unmountDisk /dev/disk2`
4. Write the image to it `sudo dd if=CentOS-7-x86_64-DVD-1810.iso of=/dev/disk2`. Noticed that centOS is much better than Ubuntu and this steps takes a while (3-4h).

## Step 2：Installation
From now on follow the actual [post](https://www.tecmint.com/centos-7-installation/).

F12 for Lenovo to be able to select USB for booting.

First warning comes with disk space. Deleted one of the partition. Needed about 100G.

Everything else is intuitive except that in the post it used __minimal install__ which is equivalent to server version of Ubuntu. Selected `Gnome` version since this was done on an laptop for personal uses only. This explains why the file was so big. It contains all versions of centOS.


