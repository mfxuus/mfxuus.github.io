---
title: "Neural Networks - 3. Convolutional Neural Networks: Concepts and Details"
excerpt: "In this third part in our Neural Network series, we will explore one of the most important classes of neural networks - Convolutional Neural Networks. We will build upon our previous posts, discuss the theories behind its structural components, and learn about the essential concepts that we will need to know before diving into any serious implementations."
header:
  image: /assets/images/headers/neural_network.jpg
categories:
  - Neural Networks Series
tags:
  - neural network
  - deep learning
  - convolutional neural network
toc: true
toc_h_max: 3
related: true

---



## Links to Other Parts of Series
- [Neural Network Series]({{ "Neural Networks Series" | slugify | prepend: "#" | prepend: site.category_archive.path | relative_url }})

## Overview

In this post, we will learn about the essential components that make up a typical Convolutional Neural Network. This shall lay the foundation for our future posts, where we will start implementing CNNs with example Python codes.


## What is a CNN?

What makes CNN different from a vanilla neural network? It's not hard to guess from the naming that *convolution* probably plays a critical role here. If we know anything about signal processing, or the mathematical definition of convolution, we would know that the term refers to the operation and process (and sometimes, the result of the process) of modifying one function with another, which results in a third function. In a 1 dimension example below, we see how the function `g` and `f` convolves into a third function `(f+g)(t)`.

| ![box functions convolution](/assets/gifs/neural_networks/convolution_example.gif) |
|:--:|
| *(gif <a href="https://en.wikipedia.org/wiki/Convolution">source</a>)* |


What does this means for neural networks? Recall reading in a typical input image as matrices of pixel values, a convolution on such images / matrices would mean using a second matrix, usually of a smaller dimension such as 3-by-3, and generate a new matrix by sliding this second matrix through the original one, computing dot products along the way. The animation below might help in understanding what exactly we are talking about:

| ![box functions convolution](/assets/gifs/neural_networks/cnn_convolve.gif) |
|:--:|
| *(gif <a href="https://devblogs.nvidia.com/deep-learning-nutshell-core-concepts/convolution_schematic/">source</a>)* |


Notice that this dot product could be taken over the depth of the network. That is, for an RGB image with 3 channels (and hence 3 large green matrices), each element in the convolved feature (pink matrix) may be the sum of 9 * 3 values. There are many other considerations that we will talk about in detail below, but we should get the idea now: The feature that makes CNNs unique, and different from other networks, is the use of such convolutional layers within the network. But why are we using them?


## Why Convolution?

### Dimension Reduction

Recall that in a typical, regular neural network, all nodes are connected to all other nodes in adjacent layers. Each connection, and hence each weight, would therefore become a parameter that needs to be tuned in the backpropagation phase. That might work for smaller images such as a 64 * 64 * 3 image, and with few layers, but what about an 100 * 100 * 3 image, or even larger ones? 

Let's think about how many parameters are required if we use a convolution layer, with input of 100 * 100 * 3, and a convolution filter of dimension 5 * 5 * 3. In the example above, the filter is set to 
```
[
	[1, 0, 1],
	[0, 1, 0],
	[1, 0, 1]
]
```

... but in reality, we don't know what filter would work best, so they will all be treated as parameters to be tuned. In the 3 * 3 * 1 case we had 9 parameters (ignoring a constant bias term for now), and similarly, for a 5 * 5 * 3 filter, we would need to tune 75 parameters; much fewer than training to learn all connections between all nodes. Through parameter sharing and sparsity of parameters, we are able to significantly reduce the number of parameters in our model.


### Feature Extraction

Aside from reducing the number of parameters to train, we also need the technique to actually work - that is, it needs to be able to help the algorithm understand the input image. It turns out that these filters indeed tend to specialize on detecting certain features. As discussed in an earlier post, lower level layers tend to detect simple features such as edges, while deeper layers may be able to digest more abstract features. Let us try to understand how it works through a simple example in edge detection.

| ![edge detection](/assets/images/posts/edge_detection.png) |
|:--:|
| *(<a href="http://datahacker.rs/edge-detection-extended/">source</a>)* |


In the image above, the matrix on the left represents a grey scale image with some white on the left, and grey on the right. We convolve it with a filter, which results in the matrix on the right, indicating that an edge (non-zero values larger than some threshold) have been detected around the middle of the original matrix, as it should. How did it achieve this? Let's look at the filter we have used:
```
[
	[1, 0, -1],
	[1, 0, -1],
	[1, 0, -1]
]
```

If we convolve this filter with another matrix, we notice that if original patch has approximately same values in left and right column, then the resulting value in the convolved matrix would be close to 0. Only when left and right of a particular patch is quite different, do we result in a high convolved value. In other words, the convolved matrix will have larger values at a location, if the original patch corresponding to this convolution step displays a vertical edge. 

Note that we could have multiple channels of filters, so that we could detect various features. The following animation nicely showcases the feature extraction aspect of our convolution layers, where various different filters will be activated by different features in the input image.


| ![box functions convolution](/assets/gifs/neural_networks/cnn_convolve_feature_scan.gif) |
|:--:|
| *(gif <a href="https://cs.nyu.edu/~fergus/tutorials/deep_learning_cvpr12/">source</a>)* |


## Convolution Hyperparameters

Hyperparameters are things to tune even before starting any training. For our CNNs, among other things such as learning rate (discussed in last post), there are a couple of hyper-parameters to consider that is particularly relevant to the convolutional layers: stride, padding, size, and number of filters.


### Size and Number of Filters

Nothing too complicated here, but we do need to decide on the size of the filters we intend to use. We commonly settle on filters size of 3-by-3 or 5-by-5, with corresponding depths. In terms of the number of filters to use, we usually pick a power of 2, such as 16, 32, 64, etc.

### Padding

Notice that in the green-orange-pink convolution example above, the image is 5-by-5, while the output is only 3-by-3. Just one layer into our network and we are only left with 9 values to play with. Sometimes this is not a desirable thing, and more importantly, we can also see in that example that the values in the corner cases are rarely used. To address these issues, we sometimes *pad* our layers before passing it through a convolution.

| ![box functions convolution](/assets/gifs/neural_networks/cnn_convolve_with_padding.gif) |
|:--:|
| *(gif <a href="https://stackoverflow.com/questions/52067833/how-to-plot-an-animated-matrix-in-matplotlib">source</a>)* |

The idea is really simple - add some additional values around the original matrix. What we see in the animation above is a common choice, which is to pad with 0s. Another choice that we need to make is, how much to pad? The decision made above was to do a "same padding", where the input and the output would be of the same dimension. You may also hear about "valid padding", which is essentially "no padding", since it dictates the filter to stay within *valid* positions of the original matrix. Padding also needs to take into account *strides*, which we will get to next.

### Stride

Stride controls how much to shift when we convolve the filter with the input matrix. In all the examples above, the filter has been sliding one column at a time, going through all the cells. In some cases, mostly when we have a larger input layer, we can skip over some columns to save computational complexity and not lose too much performance. The number of rows or columns we shift each step, is called *stride*. In the example below, the stride is set to 2, and we are shifting 2 columns each step we move the filter.


| ![cnn_stride](/assets/images/posts/cnn_stride.png) |
|:--:|
| *(<a href="https://www.kaggle.com/manmohan291/57-cnn-basics">source</a>)* |



## Types of Layers

### Convolution Layers

This is the layer we have been discussing so far, and is probably the most important layer in CNNs. However, as we will see, there are two more very important layers that are almost present in all CNNs.

### Pooling Layers

A Nobel Economics Prize laureate who taught me at NYU once gave the class a wise piece of advice regarding Mathematical concepts: If you don;t know it, Wikipedia is a wonderful source for such purposes. In that spirit, and extending it to deep learning concepts, let me quote again from [Wikipedia](https://en.wikipedia.org/wiki/Convolutional_neural_network#Pooling):

>Convolutional networks may include local or global pooling layers to streamline the underlying computation. Pooling layers reduce the dimensions of the data by combining the outputs of neuron clusters at one layer into a single neuron in the next layer. Local pooling combines small clusters, typically 2 x 2. Global pooling acts on all the neurons of the convolutional layer. In addition, pooling may compute a max or an average. Max pooling uses the maximum value from each of a cluster of neurons at the prior layer. Average pooling uses the average value from each of a cluster of neurons at the prior layer.

Aside from reducing dimensions of the data, pooling layers also act as a down sampling process, which helps with dealing with small variances in the input images. This is because the features extracted are from local inputs, but what if the image is shifted as bit? Pooling allows us to make such features slightly translation-invariant, since if the input is changed by a small amount, the output from the pooled layer would not change by much.

| ![cnn_stride](/assets/images/posts/maxpool.jpeg) |
|:--:|
| *(<a href="https://cs231n.github.io/convolutional-networks/">source</a>)* |


### Fully Connected Layers (FC)

A fully-connect layer is often used in the last or last few layers of a CNN. The layer is usually generated through flattening the output of the previous layer, which usually happens to be a matrix. The flattening process is essentially converting the original matrix into a 1-d array by concatenating all values. Fully-connected here means that every neuron in one layer is connected to every neuron in another layer, and a most common example is when we do image classifications, the last layer would simply be an array of values indicating the relative likelihood of the input image being in any given class.



## Regularization

![cnn_regularization](/assets/images/posts/cnn_regularization.png)

The graph above illustrates one common issue that we come across in data science, and that is overfitting. Overfitting makes our model seem to work very well on our training set, but once we start testing it on external data, the performance becomes much worse, as it has been trained to work on too specifically on the training set. In the scope of Deep Learning, the term *regularization* is used to describe techniques that aim to solve or mitigate this issue. As the Deep Learning book by Ian Goodfellow et al. writes:

> A central problem in machine learning is how to make an algorithm that will perform well not just on the training data, but also on new inputs. Many strategies used in machine learning are explicitly designed to reduce the test error, possibly at the expense of increased training error. These strategies are known collectively as regularization.

Here in this post we will briefly talk about two types of regularization, one by punishing overall weights on coefficients, and one by punishing putting too much weight on a selectively few parameters.

### L2 and L1 regularization

This type of regularizations are sometimes also known as *weight decay*, as the result of applying them is driving the weights closer to the origin. We usually achieve this by appending a regularization term to the cost function, and take it into account when performing optimization and updates. For example:

```
L1: Cost function = sum of losses + lambda * sum(abs(weights))
L2: Cost function = sum of losses + lambda * sum(weights^2)
```
Both L1 and L2 puts pressure on the model to select smaller weights, while in the case of L1, some weights might be pushed to 0, resulting in a sparser network.


### Dropout

The second type of regularization is actually more interesting. What it does is that during our training, in each iteration, some nodes are randomly removed from the network, hence the term, dropout. The result is that each iteration would be done through a different version of the model, and hence putting significant weight on a few selective nodes would not produce a good result - they will lead to large errors when the selective few nodes are chosen as the nodes to be dropped. The animation below demonstrates how it works:

![cnn_dropout](/assets/gifs/neural_networks/cnn_dropout.gif)


## Conclusion

Now that we have a good understanding of these basic concepts that make up CNNs, we are ready to study a few important CNNs developed throughout the short span of this recent decade. Case studies are important, as they will offer us insights into what works, why they work, or why they don't work. Understanding these can help us build better networks ourselves, or even if we are only interested in using and implementing these networks, it is often helpful to have a basic understanding of what we are using and importing from the libraries and pacakges.


## Resources

A very accessible introductory book on Deep Learning is as shown below. It is a fairly new book, but a pretty easy read that will give you a good idea of modern AI approaches and techniques.

<a href="https://www.amazon.com/Deep-Learning-Press-Essential-Knowledge/dp/0262537559/ref=as_li_ss_il?crid=32X3ZH6FUEI41&keywords=neural+networks&qid=1577938868&s=books&sprefix=neural+,stripbooks,180&sr=1-17&linkCode=li3&tag=mfxuus01-20&linkId=b3187aba027f2ddddc743e186c4238aa&language=en_US" target="_blank"><img border="0" src="//ws-na.amazon-adsystem.com/widgets/q?_encoding=UTF8&ASIN=0262537559&Format=_SL250_&ID=AsinImage&MarketPlace=US&ServiceVersion=20070822&WS=1&tag=mfxuus01-20&language=en_US" ></a><img src="https://ir-na.amazon-adsystem.com/e/ir?t=mfxuus01-20&language=en_US&l=li3&o=1&a=0262537559" width="1" height="1" border="0" alt="" style="border:none !important; margin:0px !important;" />

<i>Instead of asking for donation, or plaguing you with ads, I choose to provide you with links through Amazon's Affiliate programs. Your purchase through the link helps support my blog at no additional cost to you, and you're buying something truly awesome at the same time!</i>