---
layout: post
title: When Conda env Goes Crazy 
categories: [notes]
tags: [python, conda]
---

I wasn't very careful when I was creating an environment, which resulted in this new environment replacing my `base` environment!

	(base) $ conda env list
	# conda environments:
	#
		                 /home/user/build/anaconda3
		                 /home/user/build/anaconda3/envs/biobakery3
	base                  *  /home/user/build/anaconda3/envs/picrust2
	biobakery3               /home/user/build/anaconda3/envs/picrust2/envs/biobakery3
	picrust2                 /home/user/build/anaconda3/envs/picrust2/envs/picrust2
		                 /home/user/build/anaconda3/envs/py2
		                 /home/user/build/anaconda3/envs/py38

As you can see, the current environment is base (*), 
		              


You can imaging that I cannot delete this environment since now its name becomes `base`. I also cannot activate any other environment because they are now parralle to this new environment and are blind to my new `base`.

I tried to clone existing environments by:

	$ conda create clone /home/user/build/anaconda3/envs/struo2 -n struo2
	
However this 




 
 

