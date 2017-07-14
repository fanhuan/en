---
layout: post
title:  Convert abi trace file to fastq
categories: [notes]
tags: [Open lab note - Sanger sequencing]
---

install biopython
universal converter
I attened one of the [Software Carpentry](http://software-carpentry.org/) workshop (same place that I learnt about for loop in R being really inefficient), and python was 1/4 of it (about half a day). They forced us to use python 3.*!

I was still with the absolutely obslete opinion that python 3.* is not ready and we should stick to python 2.7. While in order to follow the instructor in the workshop, I started my experience with python 3.5.1 (holy crap, when did it get to 3.5?!) totally unprepared. And I was expecting the worst.

But it worked out fine. The only annoying thing I could remember is that you have to put things you want to print in paranthesis.

In python 2.*

	print "Hello World!"

While in python 3.*

	print("Hello World!")

Another thing is that python 3.* won't tolerate mixture of tab and whitespace for identation. It complains and stops working. This need a post by itself. I have so much trouble by switch between computers/platforms/text editors.
	
And the rest is just fine! I'm ready for python 3.*, I think!


*****
Two days later
*****
Damn, no Biopython for python 3.5!

Simrlls requires python 2.7...

Should I switch back to python 2.7?

****
OK now end of 2016. Still wanna move on to python 3. 
****

In python 2.* 

	for i in xrange(10):
	
In python 3.*

	for i in range(10):
	
In python 2.*, int1/int2, even if int1%int2 != 0, returns int. 

In python 3.*, always returns float, even if int1%int2 == 0.

****
Today I hit a rock. I've know this rock, long ago. I worked on it, several times, getting to know it better but always had to go around it in the end. Today I decided to really study the rock from bottom to the top and solve this PITA for good. Yes, I'm talking about, jesus, I don't even know how to frame this questions, let's call it the the switch from str object in python2 to byte-buffer object in python3. I've been struggling with this problem for more than a year now. I started to understand it better after I decided to switch to python3. However many of my users are sold on python2 and would never ever want to mess with python3. Pigheaded me wanted them to have one compatible script that would work on both versions. Cannot remember how many posts I read. I have to say [this one](http://www.catb.org/esr/faqs/practical-python-porting/) is the most helpful and sympathetic of the situation:"We don’t know what drove that design decision, but it certainly isn’t helpful." 

Text processing is all I do. But I've made up my mind to move on and tolerate python3, because it is newer. Things evolve and we cannot stop them. Let's face it, python2 is going to disappear someday, maybe in 2 years.