---
layout: post
title: Anna Karenina principle in image classification  
categories: [notes]  
tags: [Machine Learning - Image Classification -Tensorflow]
---

## Background

<img width="456" alt="figure1" src="https://user-images.githubusercontent.com/7167719/29335245-a502a92e-81cf-11e7-9dc2-b021703f6da9.png">
__Figure 1__ *The target of the mass spectrometry: bacteria carried on the upper chest of the ants (this image is taken from the original paper).*Mass spectrometry imaging is a powerful tool for detecting what type of molecules there are in a sample. However, to use this technique, you need to prepare thin tissue sections taken from the middle of a sample. My labmate, in collaboration with other scientists on campus just published a [method](https://www.ncbi.nlm.nih.gov/pubmed/28617577) where you could look at compounds on the surface of insect skeleton, without tissues sections (Figure 1). They used leaf-cutter ants as an example. Those ants are farmers and they grow fungus. The white stuff under their throats is bacteria that help they to kill bad fungus (weed). Using this technic, they were able to see what kind of molecule those bacteria produce and how they could help kill bad fungus.  

![good_bad_examples](https://user-images.githubusercontent.com/7167719/29335215-8a5843b8-81cf-11e7-8c53-88e5f41c06d9.jpg)

__Figure 2__ *Examples of successful/good or unsuccessful/bad images.*This is a very helpful technic! Except that not every mass spectrometry image was as nice as the ones showed in Figure 1. Using the skeleton directly, instead of tissue sections, has brought in lots of noise (Figure 2). Since those ants only carry that particular bacteria on their propleural plates (the parts with white stuff on in Figure 1), anywhere else that gives a signal are considered noise. In the first row in Figure 2 are three “good” ones, where the strongest signals (red) are from the propleural plates and almost no signals are from outside the ant skeleton. In the second row, there are three “bad” images.  Bad\_1 only had signal outside the ant skeleton; Bad\_2 had signal from the propleural plates but the strongest signal is still from outside the ant skeleton. Bad\_3 has the strongest signal from the propleural plates but had a lot of signal from outside the skeleton as well. This reminds me the beginning of __Tolstoy’s Anna Karenina__: Happy families are all alike; every unhappy family is unhappy in its own way. This is called Anna Karenina principle. In our case, the good ones are a like, but the bad ones could be very different. In order to proceed with her data analysis, my labmate, together with one of her collaborator, manually went through >40000 of those ant skeleton mass spectrometry images and ended up with only 30% of the images being good. Together they spent about 2 months working on manually sorting those images. Now that their paper is published as a proof of concept, my labmate is going to use this technic to generate more images, there are also other potential users of this technic, who might need to go through this task. Therefore I decided to see whether machine learning could help with this task.## Data OverviewThe raw dataset (courtesy of [authors](https://www.ncbi.nlm.nih.gov/pubmed/28617577)) contains 40804  labeled color mass spectrometry images in PNG format, including 12511 good images and 28293 images. Most images have the size of 1200 x 900 pixels.

## Algorithm

At first glance, this looked similar to Kaggle's Dogs vs. Cats competition. Therefore I followed this [post](http://www.subsubroutine.com/sub-subroutine/2016/9/30/cats-and-dogs-and-convolutional-neural-networks) and build a 9 layers convolutional neural network (CNN), including 3 convolution layers.

__Software package__: TFLearn (an API of TensorFlow)  
__Version__: Ubuntu/Linux 64-bit, GPU enabled, Python 3.6. Note that this requires CUDA toolkit 8.0 and CuDNN v5.  
__GPU__: NVIDIA GeForce GT 720 (1G of RAM)
__CNN model__:

