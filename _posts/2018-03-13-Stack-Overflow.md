---
layout: post
title:  Stack Overflow  
categories: [notes]  
tags: [unix]

---

Even if you are new to the programming world, you must have encountered stackoverflow, this wonderful website that seems to solve all your problems. However I never understood what it meant. From the logo of the site, I thought it was a metaphor of ever-accumulating tasks/papers/projects. Until today.

I was following a protocol today. One of the step involves running [trinity](https://github.com/trinityrnaseq/trinityrnaseq/wiki) and the first thing it asked me to do was:

	ulimit -s unlimited
	
This might be my first encounter with ulimit (still so new to linux after all these year!). Apparently it helps to set limits on each user account so people can share one system harmoniously. Here are things that you can control:

	$ ulimit -a
	core file size          (blocks, -c) 0
	data seg size           (kbytes, -d) unlimited
	scheduling priority             (-e) 0
	file size               (blocks, -f) unlimited
	pending signals                 (-i) 2062741
	max locked memory       (kbytes, -l) 64
	max memory size         (kbytes, -m) unlimited
	open files                      (-n) 1024
	pipe size            (512 bytes, -p) 8
	POSIX message queues     (bytes, -q) 819200
	real-time priority              (-r) 0
	stack size              (kbytes, -s) 8192
	cpu time               (seconds, -t) unlimited
	max user processes              (-u) 2062741
	virtual memory          (kbytes, -v) unlimited
	file locks                      (-x) unlimited
	 
Apparent `ulimit -s unlimited` was trying to remove the limit on stack size.

## What is stack size

According to [wiki](https://en.wikipedia.org/wiki/Stack-based_memory_allocation): "_Stacks in computing architectures are regions of memory where data is added or removed in a last-in-first-out (LIFO) manner...A thread's assigned stack size can be as small as only a few bytes on some small CPUs. Allocating more memory on the stack than is available can result in a crash due to stack overflow...In software, a stack overflow occurs if the call stack pointer exceeds the stack bound. The call stack may consist of a limited amount of address space, often determined at the start of the program. The size of the call stack depends on many factors, including the programming language, machine architecture, multi-threading, and amount of available memory. When a program attempts to use more space than is available on the call stack (that is, when it attempts to access memory beyond the call stack's bounds, which is essentially a buffer overflow), the stack is said to _overflow_, typically resulting in a program crash._"

## Why does trinity needs the stack size to be set as unlimited?

As a python programmer, I don't need to worry about stack size. But trinity is written in Again, according to wiki on [stack overflow](https://en.wikipedia.org/wiki/Stack_overflow): "_The most common cause of stack overflow is excessively deep or infinite recursion, in which a function calls itself so many times that the space needed to store the variables and information associated with each call is more than can fit on the stack._"

Does this ring any bell? Yes. There is a step called recursive in Trinity. 


