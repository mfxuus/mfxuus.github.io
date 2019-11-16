---
title: "Neural Networks - 2. Closer Look at Neural Networks"
excerpt: "In this second part in our Neural Network series, we will explore the theoritical side of Neural Networks. In particular, we will learn about the common structure of such networks, the way an input is processed through the network, how to train the network to generate better outputs, as well as common tricks to improve the process."
header:
  image: /assets/images/headers/neural_network.jpg
categories:
  - Neural Networks Series
tags:
  - neural network
  - deep learning
toc: true
toc_h_max: 3
related: true

---



## Links to Other Parts of Series
- [Neural Network Series]({{ "Neural Networks Series" | slugify | prepend: "#" | prepend: site.category_archive.path | relative_url }})

## Overview

In the [last post]({% post_url 2019-11-09-neural-networks-pt1 %}), we have given an overview of the architecture of the several important types of neural networks. In this post, we will go into more details. Starting from a very simple example, we will build up the example little by little, and in the end, we would have a solid understanding of how a basic neural network works.


## Building a Neural Netowrk

### Goal

| ![simple neural network](/assets/images/posts/simple_nn0.png) |
|:--:|
| *(<a href="https://www.wandb.com/articles/fundamentals-of-neural-networks">source</a>)* |


For a general neural network, we would include the following components: Input layer, hidden layers, and an output layer. Within each layers, are neurons / nodes, each connected to some other nodes in other layers. Depending on the task, we could have one or many inputs, and one or many outputs; the nodes, in particular, also contains important information on what we are doing with data passed in from the previous layer. Next, we will start from the very beginning, the Input Layer.


### Input Layer

Input layer contains data that we want the model to process. They are what is available to us, and what we think is relevant to the output of interest. For example, if we want to compute the sum of `n` numbers, then the input would be the `n` numbers, and the output would be adding them all up. On the other hand, if we want to try to recognize a hand-written digit in the form of a 28\*28 image, the input could be 784 dimensions, one for each pixel value, as shown below.

| ![example_nn_image_input](/assets/images/posts/example_nn_image_input.png) |
|:--:|
| *(<a href="https://www.wandb.com/articles/fundamentals-of-neural-networks">source</a>)* |


### Weights

Simply adding up `n` numbers doesn't sound too exciting. What if we allow our model to have a bit more flexibility by adding some weights? Compare the two diagrams below. Unlike the simple sum on the left, given a set of inputs, the network on the right can adjust its weights to produce different outputs. What significance does this carry, you might wonder. In fact, if we treat these weights as parameters, they are exactly the values that we can tweak to improve our network and model. Without the weights, the only way to adjust the model would be to remove or add inputs, whereas with the weights, we are able to tell the model, x<sub>1</sub> is very important, and I want to attach a high weight w<sub>1</sub> to it, and hopefully get a more accurate output. Keeping this in mind, we will come back to the weights when we discuss how we train the networks.


| ![example_nn_weights](/assets/images/posts/example_nn_weights.png) |
|:--:|
| *(Adapted from <a href="https://www.researchgate.net/figure/Structure-of-a-simple-one-layer-neural-network_fig1_316613684">source</a>)* |


### Hidden Layers

Now, how do we add even more flexibility to our model? After all, you might notice that our current model is completely linear, and one of the important tasks we want to handle with neural networks is non-linearity. How about adding some more layers into our network, to spice things up? That sounds like a great idea, so let's see how it does. Suppose the weights are as indicated on the image below, that is,

```
# layer 1
x' = w1 * x + w2 * y + w3 * z
y' = ...
z' = ...
t' = ...

# layer 2
Out
= w4 * x' + w5 * y' + w6 * z' + w7 * t'
= w4 * (w1 * x + w2 * y + w3 * z) + w5 * y' + w6 * z' + w7 * t'
= (...) * x + (...) * y + (...) * z

```

Do you notice the pattern? Yes, simply adding more hidden layers do not fundamentally change our model - the final output is still a linear combination of our original inputs `x`, `y`, and `z`, only with more parameters to tune and play with, adding to the computational complexity. What then, you might ask, should we do to break the linearity?

![example_nn 2 layer](/assets/images/posts/example_nn_2_layer.png)


### Activation Functions

The answer to the question above is that, before we pass our data to the next layer, we process them with a non-linear function, usually called an activation function. With regards to the previous example, if we denote the activation function as `a(.)`, the previous example becomes:

```
# layer 1
x' = w1 * x + w2 * y + w3 * z
y' = ...
z' = ...
t' = ...

# layer 2 after activation
Out 
= w4 * a(x') + w5 * a(y') + w6 * a(z') + w7 * a(t')
= w4 * a(w1 * x + w2 * y + w3 * z) + w5 * a(y') + w6 * a(z') + w7 * a(t')
```

![example_nn activation](/assets/images/posts/example_nn_2_layer_activation.png)


And we notice that the output is no longer restricted by linear combinations of original inputs. The next natural question is then, what type of functions can we use as activation functions? As we will see later in the training part, among other things, it is quite important for the activation functions to have nice derivatives. A couple of common activation functions are listed below:
1. ReLU (Rectified Linear Unit);
2. Sigmoid;
3. TanH.

| ![example_nn_weights](/assets/images/posts/activation_functions.png) |
|:--:|
| *(Adapted from <a href="https://en.wikipedia.org/wiki/Activation_function">source</a>)* |


### Learning Weights / Training Model

Now that we have the basic structure setup, and *given some weights*, we can generate an output from a set of inputs, going through various layers and activation functions. Are we expecting to get a perfect output right from the beginning? It would be quite nice, but given some random initial weights, more likely than not the output is nothing like what are hoping for. The final task is then to learn the best weights that will hopefully generate nice outputs most of the time. When we talk about "best" weights, we are internally assuming a metric, or a ranking system. What makes one set of weights better than another? This is where Cost Functions come in.

#### Cost Functions / Loss Function / Error

Cost functions are used to measure how far away we are from our desired output, averaged over the entire training set. Loss function is the same concept applied on a single data point. Say we want to predict the price of a house, using the x, y, z model defined above. We can have x as `# of rooms`, y as `size of house in square feet`, and z as `zipcode` (perhaps a categorical variable). Then the output we are interested in shall be the `housing price`. Let's say we have a dataset used for training, consisting of Data<sub>i</sub> = (x<sub>i</sub>, y<sub>i</sub>, z<sub>i</sub>, price<sub>i</sub>) for i = 1, 2, 3, ..., 100 houses, and we apply our model to the training dataset, which generates predicted prices output<sub>i</sub> for i = 1, 2, ..., 100. What might be a sensible Cost Function here? Recall that we want to measure the distance between our model and the desired output, so that among many choices, one might consider using the following fomula: 


cost = sum<sub>i = 1,2,.., 100</sub>((output<sub>i</sub> - price<sub>i</sub>)<sup>2</sup>) / 100


In fact, this cost function is commonly known as the Mean Squred Error (MSE), as it is the mean of the squared errors, where errors are the difference between true prices and predicted prices. There are many variations of Cost Functions that are commonly used, such as `cross-entropy cost`, `exponential cost` and etc. We will introduce them more formally as we encounter them in examples in later posts.

#### Backpropagation and Gradient Descent

Now that we have a goal (minimizing cost function), we need to think about how to best achieve this. We understand that given the inputs, chosen layers and activation functions, the output, and therefore the value of the cost function, is determined by the parameters / weights. Hence, to minimize cost function is all about finding the best parameters for our network. A common technique used in neural networks is through backpropagation and gradient descent. Backpropagation propagates the error from the output layer, all the way back to every other node in the network, which is used to compute the gradient with respect to each weight. As we quote here from [Wikipedia](https://en.wikipedia.org/wiki/Backpropagation):

>The backpropagation algorithm works by computing the gradient of the loss function with respect to each weight by the chain rule, iterating backwards one layer at a time from the last layer to avoid redundant calculations of intermediate terms in the chain rule.


Once we have the gradient, we often use gradient descent (among other methods), to update the weights. Again, quoting [Wikipedia](https://en.wikipedia.org/wiki/Gradient_descent):
>Gradient descent is a first-order iterative optimization algorithm for finding the minimum of a function. To find a local minimum of a function using gradient descent, one takes steps proportional to the negative of the gradient (or approximate gradient) of the function at the current point.

![gradient_descent_andrew_ng](/assets/images/posts/gradient_descent_andrew_ng.PNG)

Above is a screen shot from Andrew Ng's popular Coursera course on Deep Learning. It illustrates gradient descent in a 2-D setting. Notice that if we start from a point (weight) on the right hand side, the gradient (slope) is positive, and gradient descent would guide us to move towards a smaller weight since `w_new = w_old - alpha * gradient`. Similarly, if we start from a lower weight, in which case we end up with a negative slope, we would update our weights in the upward direction. Both correctly moves us towards the minimal point in the function `J(w)`, assuming we have picked appropriate learning rates.

Therefore, at each node, we try to minimize the Cost Function by updating the local weights in the direction of the negative of the gradient, hoping that it would indeed lead to a lower loss. Note that gradient descent, like many other numerical methods, does not guarentee to find the golbal minimum - among many other limitations, the method might get stuck in a local minimum. The following equations summarize the usual process ata high level.


![backprop_simple](/assets/images/posts/backprop_equations.png)

Here, feedforward refers to the process where the input data flows through the network, going through nodes and activation functions, and generating an output. Once we have the output, and we can backpropagate the error from the last layer `L` - notice that in step 4, the error term in layer `l` is computed from weights from layer `l+1`, *the derivative* of the activation function in layer `l+1`, error term from layer `l+1` and z<sup>`l`</sup>(raw input passed from layer `l`) - that is, the computation is done starting from the last layer, iteratively to the first layer. Notice that if the activation function has derivative of 1 (such as positive part of ReLU), the error of the previous layer node is simply a weighted average of all the errors in the nodes it is connected to in the next layer. Finally, once we have propagated all the error terms, we can use chain rule and calculate the gradients.

Once we have the gradient, we know the direction we want to update our weights, potentially by applying gradient descent method. However, we also need to decide how much to update. In the image below, we denote this the Learning Rate - a higher learning rate implies a larger step in terms of weight updates - thay may result in unstable training, where we are constantly "overshooting" the target; the opposite is too low a learning rate, in which case it takes too long to converge to a minimum, or we might even fail to converge. For example, in the 2-D example above, we might end up zigzagging between the left and right of the minimal point, but never getting there and converging to a solution. Therefore, tuning the learning rate is a very important step in practical Neural Networks, which we will touch on more when we implement code examples in later blog posts.

| ![backprop_simple](/assets/images/posts/backprop_simple.png) |
|:--:|
| *(<a href="https://hmkcode.com/ai/backpropagation-step-by-step/">source</a>)* |

Equipped with Backpropagation and Gradient Descent, we are able to update our weights after each round of training. Take our 100-house example - suppose we start with random initial weights, thn after feeding the network all 100 housing data, we would update these weights according to our calculations. Then, we would put the 100 houses through the network again, but with these updated weights. We repeat this process for many rounds, until we get an acceptable model, or if we realize that the model is perhaps not constructed so well to begin with.


## Conclusion

Now that we have covered these essential steps, we can take a look at the bigger picture and review what we have achieved. We started from a simple network, added hidden layers and non-linearity, learned how to define a goal, how to train our model, and how to improve the weights and predictions. These are the building blocks of neural networks, with many intricacies that we have skimmed over, but will discuss in more detail when we encounter them. There is one last note I would like to make. Recall earlier when I mentioned how activation functions are selected, I noted that having nice derivatives is a desirable property. Where did we take advantage of that? Look back at the equations for backpropagation, and observe that in step 4, we have the derivative of the activation function. Having nicely behaved activation functions therefore facilitates backpropagation both in terms of convergence behavior, and in terms of computational complexity.