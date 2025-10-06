---
title: 计算机视觉 W40 Training ConvNet (1)
author: tinsir888
date: 2023/10/2
cover: /img/AUcv.jpg
katex: true
tags:
  - 笔记
  - 计算机视觉
categories:
  - AUDatalogi
  - 计算机视觉
abbrlink: 7151c568
---

# Review

cnn: sparse interations and parameter sharing

computational graphs: automatic differentiation

## Training pipeline

- set up network architecture = computational graph
- train using mini-batch stochastic gradient descent
- loop
  1. sample a batch of images
  2. forward propagation through the network and get loss
  3. back propagation to calculate the gradient
  4. update parameters using the gradient

# Activation Function

## Sigmoid

$\sigma(x)=\frac{1}{1+e^{-x}}$

nice interpretation as a saturating firing rate of a neuron

3 problems:

1. saturated neurons kill the gradients

   $\frac{\partial\sigma}{\partial x}\simeq0$ if $|x|$ is large.

2. sigmoid outputs are not zero-centered

   gradient is either all positive or all negative

3. exponential function is computational expensive

## Tanh

aka hyperbolic tangent

squashes numbers to range $[-1,1]$.

outputs are zero-centered.

but still kills gradient when saturated.

## ReLU

aka rectified linear unit

$f(x)=\max(0,x)$

does not saturate

very computationally efficient

converges much faster than sigmoid/tanh in practice

disadvantages:

1. not zero-centered output

2. no gradient flow when x<0 causing the neuron to die

   solution: Leaky ReLu $f(x)=\max(\alpha x,x),\alpha\lt1$

   - doesn't saturate
   - computationally efficient
   - converges faster than sigmoid/tanh in practice
   - will not die

## Tips when...

using ReLU, be careful to learning rates

using Leaky ReLU

using tanh, but don't expect much

don't use sigmoid, except at output layer, want values that reflect probabilities.

# Data Preprocessing

为什么我们喜欢均值为 0 的数据？

因为更好拟合！

如何获取均值为 0 的数据？

> 预处理，让一组数据的均值变为0
>
> mean subtraction，所有的数据减去均值
>
> normalization，让数据变成更加规整，**处于几乎相同的范围（almost the same scale）**

## PCA

aka principal component analysis 主成分分析

但是并不常在 CNN 中使用

# Weight Initialization

## Pitfall: all-zero initialization

- reasonable to assume half weights +, half -.
- all-zero is not reasonable, while it makes all parameters same gradient.

but it's safe to initialize the biases to 0.

## Small random numbers

initialize the weights of the neurons to small random numbers.

neurons will compute distinct updates.

`W=0.01 * np.random.randn(D,N)`

why is weight initialization important?

> layer activation outputs either explode or vanish
>
> loss gradients will either be too large or too small to flow backwards

forward pass simply entails performing consecutive matrix multiplications.

How can we find the sweet spot?

> To prevent the gradients of network's activations from vanishing or exploding, we ensure that
>
> 1. the mean of the activations in any given layer should be 0
> 2. variance of the activations should stay the same among all layers

## Xavier initialization

$a^{(j+1)}=\tanh(W^{(j)}a^{(j)}+b^{(j)})$

$Var(a^{(j+1)})=s_j\cdot Var(W^{(j)})\cdot Var(a^{(j)})$

to make variance stay the same, we have:

$Var(W^{(j)})=\frac{1}{s_j}$.

## In practice

improper weight init cause vanishing or exploding gradients

don't init with 0s or constants!

# Batch Normalization

motivation: small changes to the weights have huge impact other neurons, make them saturation and vanishing gradients

internal covariance shift, to solve this:

- traditional solution: ReLU

- new solution: make the distributions stable by enforcing zero mean and unit variance. aka **batch normalization**

## Basic idea

standard score $z=\frac{x-\mathbb E(x)}{\sqrt{Var(x)}}=\frac{x-\mu}{\sigma}$

batch normalization:

input: $X\in\mathbb R^{N\times D}$

N is num of batch, D is dimensionality of the data.

X is normalized during training as $Z=\frac{X-\mu}{\sqrt{\sigma^2+\epsilon}}$, where

$\mu=\frac{1}{N}\sum_ix_{ij}$ for $j=1,\cdots,D$

$\sigma^2=\frac{1}{N}\sum_i(x_{ij}-\mu)^2$ for $j=1,\cdots,D$

to accomplish this, for each dimension, a pair of learnable parameters $\gamma_j,\beta_j$.

$Y_{ij}=\gamma_jZ_{ij}+\beta_j$, where $\gamma_j=\sigma_j,\beta_j=\mu_j$

N is the batch size

## Other variants

layer normalization

instance normalization

group normalization



The batch normalization layer usually **after full-connected or conv layer** and **before nonlinearity**.

### Batch-Norm enable higher learning rates

prevent small changes to the params from amplifying into larger and suboptimal changes in activations in gradients. i.e. prevents the training form getting stuck in the saturated regimes of nonlinearities.

it also makes training more resilient to the param scale.

## In practive

- always use batch normalization
- insert batch norm layers before non-linearities
- makes deep networks much easier to train
- improves gradient flow
- allows higher learning rates, faster convergence.
- networks become more robust to init
- act as regularization during trainnig

# Summary

- Activation function: Use (Leaky) ReLU
- Data preprocessing: Subtract mean
- Weight initialization: Use Xavier/Kaiming
- Batch Normalization: Use always