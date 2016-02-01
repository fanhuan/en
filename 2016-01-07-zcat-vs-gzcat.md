---
layout: post
title:  zcat vs gzcat
categories: [notes]
tags: [Open lab note - OS X]
---
Usually we use zcat to check gz files. While today when I was trying to do the same, something strange happend:

	Huan$ zcat /Users/Huan/Documents/filename.gz | head
	zcat: can't stat: /Users/Huan/Documents/filename.gz (/Users/Huan/Documents/filename.gz.Z): No such file or directory

Of course there is a filename.gz in my Documents directory. However, there is no filename.gz.Z there. But why it is requesting something like that? 

It turns out that there's some discripency between linux and osx (like the carrige returns or anything else!) and that could mess things up. According to [this post](http://serverfault.com/questions/570024/zcat-gzcat-works-in-linux-not-on-osx-general-linux-osx-compatibility]), "In osx, zcat seems to automatically append .Z to the file name.", which seems to be what's happening here. But I've been zcating .gz files ALL THE TIME and it is the first time that something like this happend. 

The solution is to use gzcat instead of zcat and it worked. But I'm still pretty confused. 
