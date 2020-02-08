---
title: "XGBoost - Gradient Boost"
excerpt: "In this first section of our Machine Learning series, we will talk about a popular machine learning technique for regression and classification problems, namely XGBoost. As usual, we will start from the basics, and start building towards the fully featured model. This post will cover Gradient Boost."
header:
  image: /assets/images/headers/xgboost.jpeg
categories:
  - Machine Learning Series
tags:
  - xgboost
  - decision trees

toc: true
toc_h_max: 3
related: true

---



## Links to Other Parts of Series
- [Machine Learning Series]({{ "Machine Learning Series" | slugify | prepend: "#" | prepend: site.category_archive.path | relative_url }})


## Prerequisites
- [Decision Tress]({% post_url 2020-01-18-machine-learning-decision-trees %})
- [Random Forest]({% post_url 2020-01-25-machine-learning-random-forest %})
- [AdaBoost]({% post_url 2020-02-01-machine-learning-adaboost %})


## Overview

If you're not familiar with Decision Trees, Random Forests, and / or AdaBoost, and haven't already checked out the blog posts linked above, please check that out first before proceeding with this post on Gradient Boost, since this does build upon the former posts.

Now that we got that covered, let's dive right in!


## What is Gradient Boost?

Gradient Boost is ...

>Gradient boosting is a machine learning technique for regression and classification problems, which produces a prediction model in the form of an ensemble of weak prediction models, typically decision trees. It builds the model in a stage-wise fashion like other boosting methods do, and it generalizes them by allowing optimization of an arbitrary differentiable loss function

In some sense, AdaBoost can be viewed as a special case of the general Gradient Boosting algorithms, but in common applications, they are quite different indeed. They are similar in that they both construct a series of trees (usually shallow ones) and make an ensemble out of them, but while AdaBoost does so by explicitly setting higher weights for previously misclassified samples, Gradient Boost uses the gradient to nudge the predictions towards the true values. While AdaBoost may be more intuitive to understand, without referencing concepts such as gradients, Gradient Boost are more flexible, and can be modified to work with a variety of loss functions, as long as their gradient is well behanved.

## Steps

We will base our discussion off of the steps listed below:

<table>
  <thead style="display: inline-block; width: 100%;">
    <tr style="display: inline-block; width: 100%;">
      <th style="text-align: center; display: inline-block; width: 100%;">
        <a href="/assets/images/posts/xgboost/gradient_boost/steps.png" class="image-popup">
            <img src="/assets/images/posts/xgboost/gradient_boost/steps.png" alt="Gradient Boost Steps">
        </a>
      </th>
    </tr>
  </thead>
  <tbody style="display: inline-block; width: 100%;">
    <tr style="display: inline-block; width: 100%;">
      <td style="text-align: center; display: inline-block; width: 100%;">
        <em>(source: <a href="https://en.wikipedia.org/wiki/Gradient_boosting">Gradient boosting - Wikipedia</a>)</em>
      </td>
    </tr>
  </tbody>
</table>

Here, `x` will be the input features (think height, weight, gender, etc.), and `y` will be the target variable (say, health index). Each subscription `i` would denote a sample from our data set. For Loss Function `L(y, F(x))`, which takes the true value `y` and the predicted value from x `F(x)`, we will use a particular (but very commonly used) Loss Function: the MSE, Mean Squared Errors, which is the sum of the squared differences / errors between true and predicted values.


### 1. Initialization

The first step is to initialize some predictions (common for all samples, since gamma here is independent of `i`) in a reasonable fashion - here we specify the gamma to be the one to minimize our Cost (sum of losses). If we use MSE as our Loss function, it is easy to derive that the initial `gamma` = `F0(x)` = `average of all y's`:

To minimize `SUM (y_i - gamma)^2` with respect to gamma, we take the derivative, and set it to 0, which leads us to `SUM (y_i - gamma) = 0`, so that `SUM gamma's = SUM (y_i)`, where `SUM gamma's = # of y_i's * gamma`, and that leads to `gamma = SUM (y_i) / (# of y_i's) = averages of y_i's`.


### 2/a. Computing "pseudo-residuals"

Here in step 2, we are in an iteration for each tree we plan to constrcut (as the formula specifies, we plan to construct `M` trees to make up our eventual ensemble).

The pseudo-residual is defined as the partial derivatives of our Loss function (with respect to our prediction `F`) evaluated at our previous prediction. For our MSE loss function, and take our first iteration and first sample for example (m=1, i=1), we have:`r_11 = F(x_1) - y_1`, where we have set `F(x_1) = F0(x_1) = average of y_i's`. This is not that different from the residuals we are used to, but particularly because we have chosen the MSE as the loss function. The crucial part here is to recognize this as a Gradient, but which just happens to have an intuitive meaning.


### 2/b. Fit a weak learnner to the residuals

This step is usually a standard DT training, but where we can specify the depth and leaves we want the trees to have. Usually it is quite a shallow tree, since we will train `M = quite large` number of trees anyway.

### 2/c. Compute a tree-specific learning rate

The Wiki steps stated the general term of multiplier, but it is essentially our learning rate. To pick this learning rate, we try to solve another optimization problem, where we choose the learning rate that results in the least cost when taking into consideration the newly constructed tree.

### 2/d. Update the Model

Now we simply append our new tree to our existing model, multiplied by the optimal learning rate as specified in the previous step.

### Loop

We perform steps 2/a to 2/d for each tree we want to construct, and our final output would be the initial guess `F0`, modified by a series of Decision Trees, each with a specific learning rate.


## Why does it work?

It doesn't seem hard to grasp the mechanical steps to perform such an algorithm, but intuitively, why does it work? In some sense, it is not that different from AdaBoost - in each step, we are focusing more on the "residuals", the part that our previous model was not able to effectively account for, similarly to adding more weights to misclassified samples in AdaBoost. The animation below might provide some further insights:

<table>
  <thead style="display: inline-block; width: 100%;">
    <tr style="display: inline-block; width: 100%;">
      <th style="text-align: center; display: inline-block; width: 100%;">
        <a href="/assets/images/posts/xgboost/gradient_boost/boosted_stumps.gif" class="image-popup">
            <img src="/assets/images/posts/xgboost/gradient_boost/boosted_stumps.gif" alt="Animation of Gradient Boosting">
        </a>
      </th>
    </tr>
  </thead>
  <tbody style="display: inline-block; width: 100%;">
    <tr style="display: inline-block; width: 100%;">
      <td style="text-align: center; display: inline-block; width: 100%;">
        <em>(source: <a href="http://uc-r.github.io/gbm_regression">Gradient Boosting Machines - UC Business Analytics R Programming Guide</a>)</em>
      </td>
    </tr>
  </tbody>
</table>

Here we are observing the model going through each iteration. Starting from a single horizontal line, we have predicted `F0`, something like the average's of `y_i`'s. With each additional tree being added, a new vertical line is being displayed, and a shift in the prediction modified by the newly added tree. With more and more trees being added, our prediction is looking more and more like the `True function`. 


## Overfitting?

We might worry about overfitting, although ensemble models are in general better off in this regard, in practice Gradient Boosting still suffers from overfitting. There are quite a few things to tune during cross-validation steps in regards to that. For example, we can add shrinkage on the learning rate, so that each individual trees contribute less to the output. For more discussion on overfitting, I would recommend [this article](https://jeremykun.com/2015/09/21/the-boosting-margin-or-why-boosting-doesnt-overfit/) which goes into quite some details.



## Afterthoughts

For those of you familiar with gradient descent, the whole gradient boosting thing might look familiar to you. So what is special about Gradient Boosting? This wonderful resource, [Gradient boosting: frequently asked questions](https://explained.ai/gradient-boosting/faq.html), makes a clear and concise summary:

>Gradient descent optimization in the machine learning world is typically used to find the parameters associated with a single model that optimizes some loss function, such as the squared error. In other words, we are moving parameter vector around, looking for a minimal value of loss function that compares the model output for to the intended target.

>In contrast, GBMs are meta-models consisting of multiple weak models whose output is added together to get an overall prediction. GBMs shift the current prediction [...] around hoping to nudge it to the true target. The gradient descent optimization occurs on the output of the model and not the parameters of the weak models.


>Getting a GBM's  approximation to  is easy mathematically. Just add the residual to the current approximation. Rather than directly adding a residual to the current approximation, however, GBMs add a weak model's approximation of the residual vector to the current approximation. By combining the output of a bunch of noisy models, the hope is to get a model that is much stronger but also one that does not overfit the original data. This approach is similar to what's going on in Random Forests.


In some sense, Gradient Boosting is performing gradient descent in the function space of our prediction functions.


## Conclusion

We are now finally well-prepared and ready for our end game - XGBoost. Tune in for our next post on the popular machine learning algorithm, and learn about the theories and applications, explained clearly!


## Resources

<a href="https://www.amazon.com/gp/product/1449369413/ref=as_li_ss_il?ie=UTF8&linkCode=li3&tag=mfxuus-20&linkId=f231453b00c1cb5bbb3e02a5cdf7fdea&language=en_US" target="_blank"><img border="0" src="//ws-na.amazon-adsystem.com/widgets/q?_encoding=UTF8&ASIN=1449369413&Format=_SL250_&ID=AsinImage&MarketPlace=US&ServiceVersion=20070822&WS=1&tag=mfxuus-20&language=en_US" ></a><img src="https://ir-na.amazon-adsystem.com/e/ir?t=mfxuus-20&language=en_US&l=li3&o=1&a=1449369413" width="1" height="1" border="0" alt="" style="border:none !important; margin:0px !important;" />

<i>Instead of asking for donation, or plaguing you with ads, I choose to provide you with links through Amazon's Affiliate programs. Your purchase through the link helps support my blog at no additional cost to you, and you're buying something truly awesome at the same time!</i>



## References
1. [Peter Prettenhofer - Gradient Boosted Regression Trees in scikit-learn (Video)](https://www.youtube.com/watch?v=IXZKgIsZRm0)
2. [Gradient boosting: frequently asked questions](https://explained.ai/gradient-boosting/faq.html)
