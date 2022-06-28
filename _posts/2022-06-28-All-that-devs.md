---
layout: post
title: All that devs
categories: [notes]
tags: [R]
---

GitPages has stopped deploy my posts for a while and I didn't know why. I was able to see the problem using the debugging mode from the Action, and magically by deleting a file that I didn't create, it seems to be (partially) working now, with some posts visible and some not. Oh my. Maybe this will take me another 2 years to figure out what is going on. 

Anyways. Today I'd like to talk about what to do when you did more `dev.off()` in R than you need to and cannot get back to `RStudioGD` aka plotting in the lower right corner of your RStudio console.

### Step 1. To save a plot in a file.

You can save it to specific format, say pdf.

	> pdf(filename, width, height)
	> plot(x,y)
	> dev.off() # shuts down the specified (the current one if not specified) device.
 
This would return 

	RStudioGD
			2
in the console. This means you will be able to see your plot in the console again.

###  Step 2. Somehow you messed up.

You might have forgot to run `dev.off()` or did it twice, anyways, you cannot plot to the console anymore. What do you do?

	> dev.new() # start a new device; default is RStudioGD
	> dev.cur() # check out the current device
	RStudioGD
			2

OK all good again.




