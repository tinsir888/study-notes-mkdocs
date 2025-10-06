---
title: 计算机视觉 W36 Machine Learning fundamentals
author: tinsir888
date: 2023/9/4
cover: /img/AUcv.jpg
katex: true
tags:
  - 笔记
  - 计算机视觉
categories:
  - AUDatalogi
  - 计算机视觉
abbrlink: f2bea2ad
---

# Linear regression

goal: build a model take a vector X as input and predicts the output Y. *Output is a linear function of the input*

## Model and loss function

model: $h_w(x)=\sum_{j=1}^mw_jx_j=w^Tx$

find a choice of w s. t. $h_w(x^{(i)})$ is as close as possible to $y^{(i)}$

search for w that minimizes the L2 norm:

loss function: $J(w)=\frac{1}{2}\sum_{i=1}^n(h_w(x^{(i)}-y^{(i)})^2=\frac{1}{2}(w^Tx^{(i)}-y^{(i)})^2$

> Why multiply a coefficient 1/2? Because there will be a coe 2 in differentiation.

## Gradient descent

how to minimize val of loss function?

1. Random search - very bad idea :negative_squared_cross_mark:

2. gradient descent - progress is slow but steady :white_check_mark:

- $w^{k+1}=w^k-\alpha\nabla J(w^k)$

- α: learning rate/step size

- j-th element of $\nabla J=\frac{\partial J(w)}{\partial w_j}=\sum_{i=1}^nx_j^{(i)}(h_w(x^{(i)})-y^{(i)})=\sum_{i=1}^nx_j^{(i)}(w^Tx^{(i)}-y^{(i)})$

  (according to the *chain rule* $\frac{\partial J(w)}{\partial w^T}\times\frac{\partial w^T}{\partial w_j}$)

limitations of gradient descent

- low speed (speed up: momentum...)
- local minimum (may fail to find a global min)

Learning rate

- very high lr: loss grow up unstop
- high lr: loss fall, but stop at a pretty high val
- low lr: loss may fall, very slow, hard to convergent
- good lr: loss fall, convergent to a relatively low val

under-fitting & over-fitting

# Hyper-parameters

a param whose val is set before the learning process.

e. g. degree of polynomial, learning rate...

## Hyper-param search

idea 1: choose hyper-param that work best on **all data**

idea 2: split data into **train** and **test** sets, choose hyper-param that work best on test data

idea 3: split data into **train**, **validation** and **test** sets, choose hyper-param that work best on validation set and evaluate test better.

idea 4: **cross-validation**, split data into folds, try each fold as validation and average the results. (useful for small datasets, but not used too frequently in deep learning)

# Logistic Regression

classifier: use features to distinguish between 2 or more classes

regressors: use features to predict some functional relationship

logistic regression is **classification** algorithm.

## Model

goal: to predict one of two possible labels, s. t. $y^{(i)}\in{0,1}$

candidate solution:$h_w(x)=1\ if\ w^Tx\gt 0\ otherwise\ 0$

sigmoid (or logistic) function: $\sigma(z)=\frac{1}{1+e^{-z}}$, interpret $h_w(x)$ as a probability.

$h_w(x)=P(y=1|x)=1-P(y=0|x)$

convention: if > 0.5, let y be 1, else let y be 0.

## Loss function

cross-entropy $J(w)=-\sum_{i=1}^ny^{(i)}\log(h_w(x^{(i)}))+(1-y^{(i)})\log(1-h_w(x^{(i)}))$

when $y^{(i)}=1$ we need $h_w(x^{(i)})\simeq 1$ to minimize the loss

why negative?

> We want loss to be positive. log lie in range from 0 to 1 where log is negative.

Optimization

the same as linear regression

Loss function derivation

$P(y=1|x)=h_w(x)$ $P(y=0|x)=1-P(y=1|x)=1-h_w(x)$

$P(y|x)=(h_w(x))^y(1-h_w(x))^{1-y}$

Loss function $L=\prod_{i=1}^nP(y^{(i)}|x^{(i)})$

（这两页幻灯片看不太懂……）

# Regularization

Weight Decay

in order to solve an ill-posed problem or to prevent over-fitting

more different approaches to solve these problems

- data augmentation
- early stopping
- dropout
- batch normalization
- weight decay
- sparsity

loss $J(w)$ plus with an extra term $J_{reg}(w)=J(w)+\lambda R(w)$, where R(w) is either the L1-norm or L2-norm of w.

𝜆 determines the trade-off between minimizing the data loss and minimizing the model parameters 𝑤.

avoid over-fitting, don't fit noise in the data.

by keeping some weights small, the regularization term make the model simpler, thereby avoid over-fitting.

## L1 vs L2

$L_1=\sum_i|w_i|$

- sparse solutions (make many entries of w = 0)
- feature selection
- \[1,1,1,1\] → \[1,0,0,0\] or \[0,1,0,0\] or \[0,0,1,0\] or\[0,0,0,1\] (prefer many zero-weights)

$L_2=\sum_iw_i^2$

- faster than L1 regularization
- aka weight decay
- non-sparse
- \[1,1,1,1\] → \[0.25,0.25,0.25,0.25\] (prefer to spread the weights)

# Softmax regression

multi-class classification

idea: convert feature vector x' into class probabilities? use **softmax** function

search for weights that minimize the difference between

- output vector of predicted probabilities
- target vector of true probabilities (one-hot vector)

there are K classes

$h_w(x)=[P(y=1|x)\cdots P(y=K|x)]^T=\frac{1}{\sum_{j=1}^K\exp(w_j^Tx)}[\exp(w_1^Tx)\cdots\exp(w_K^Tx)]^T$

why $\exp(w_j^Tx)$ instead of $w_j^Tx$?

> Probabilities must be non-negative. Applying exp ensures that numbers are positive.

what's the purpose of $\sum_{j=1}^K\exp(w_j^Tx)$?

> It normalizes the probability distribution, so that  it sums to 1.

## loss function

indicator function: 1\{true statement\} = 1, 1{false statement}=0

$J(W)=\sum_{i=1}^n\sum_{k=1}^K{\bf1}\{y^{(i)}=k\}\log(\frac{\exp(w_k^Tx^{(i)})}{\sum_{j=1}^K\exp(w_j^Tx^{(i)})})$

**:warning:only one term in the inner sum will be non-zero**

optimization

cannot solve for minimum of J(W) analytically, thus we resort to an iterative optimization algorithm

$\nabla J(w_k)=-\sum_{i=1}^n[x^{(i)}({\bf1}\{y^{(i)}=k\}-P(y^{(i)}=k|x^{(i)}))]$ for each k=1, 2, ..., K

$\nabla J(w_k)$ is a vector, has to be calculated for all K

There are some hard cases for a linear classifier

- 两类交叉
- 1类包围2类包围1类
- 1类中包围多个2类

solution

- 极坐标变换

- 神经网络

  不能用 softmax 回归解决，因为其仍然算是线性的，所以需要神经网络

# K-Nearest Neighbors

image classification

- basic nearest neighbor classifier

- distance metrics to compare images: L1 Norm, N2 Norm

- train: O(1), predict: O(N).

  bad! we only accept classifier that are fast at prediction, slow for training

Instead of copying label from nearest neighbor, take **majority vote** from K closest points.

## Hyper-parameters of KNN

1. The best value of K?
2. best distance metric?

problem-dependent!!!

## Poor choice of features

- KNN is never used on raw pixel intensities
- very slow at test time
- distance metrics on pixels are not informative

## Curse of dimensionality

In high dim data, all obj appear to be sparse and dissimilar in many ways. image classification based on KNN performs poorly when features are raw pixels.

search becomes slow as dimensionality increases.

## Preview of CNN

instead of using KNN: use a pre-trained CNN to obtain a better feature representation.

non-linear transformation of pixel values into a presentation that is optimal for classification

transfer learning

- pretrained encoder
- finetune weights of decoder: linear classifier performing softmax regression

# Cluster analysis

vector in the same group (a cluster) are more similar to each other

e. g. K-Means
