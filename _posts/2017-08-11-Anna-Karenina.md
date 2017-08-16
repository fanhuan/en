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
__Network Architecture__(from the same [post](http://www.subsubroutine.com/sub-subroutine/2016/9/30/cats-and-dogs-and-convolutional-neural-networks)):  
1: Convolution layer with 32 filters, each 3x3x3  
2: Max pooling layer  
3: Convolution layer with 64 filters  
4: Convolution layer with 64 filters  
5: Max pooling layer  
6: Fully-connected 512 node layer  
7: Dropout layer to combat overfitting  
8: Fully-connected layer with two outputs  
9: Regression 
### Test 1: uneven dataset ([code](https://github.com/fanhuan/ImageClassification/TensorFlow_Ant.ipynb))
I split my data into 80% training, 10% validation and 10% testing. As you can see I only have 1G of GPU RAM and I quickly ran into ResourceExhaustedError during the training (batch_size=100). I decreased the batch_size to 50, and only trained for 10 epochs. I got an accuracy of __79.8%__ with cross-validation.

### Test 2: balanced dataset ([code](https://github.com/fanhuan/ImageClassification/TensorFlow_Ant_balanced.ipynb))
I looked at the dogs vs. cats dataset again, how convinient that the dogs and cats have exact the same number of pictures! Then I realized that if I don't give the model the same amount of pictures from the two categrories to train, it might overfit one category while fail to extract important features from the other. So I made a balanced dataset with 3672 pictures from good and 3672 from bad (90% training and 10% validating). I also increased the batch size to 100 since now I'm running a smaller dataset. The accuracy increased to __98.58%__! When used on the test dataset (3672 pictures with 1250 good and 2829 bad ones), the accuracy on the __good__ ones were __100%__! However the accuracy on the __bad__ ones were still __80%__. __Anna Karenina principle__ again is in the way.

### Test 3: yes binary classification.
I was actually not too discouraged by the result of test 2. Afterall, if we could get rid of a large amount of bad pictures (the false negative rate was 0 in that dataset), it already would drastically reduced the burdon on my labmate. But I did not understand why given a balanced dataset, the model ends up so biased towards the good ones.

Then I realized that this is not a dogs vs. cats problem. This is a dogs vs. non-dogs problem (sorry cat people)！ 
