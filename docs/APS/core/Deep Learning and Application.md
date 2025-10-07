---
title: APS 重点复习课程之深度学习及应用
author: tinsir888
date: 2022/10/27
cover: /img/aps.png
katex: true
tags:
  - APS
  - 德国
  - APS 重点复习课程
  - 深度学习
  - 留学
categories:
  - 留学申请
  - APS 审核复习
abbrlink: a942a1b8
---

# Overview

Gradient Vanishing/Exploding

Gradient Descent

- Batch Gradient Descent
- Stochastic Gradient Descent
- Mini-batch Gradient Descent

Overfitting+Underfitting

Backpropagation

Useful Data Argumentation Method

Active Function

Several Network

- CNN
- RNN, LSTM
- Generative Models

# Gradient Vanishing/Exploding

Active function (sigmoid) + depth of the network:

Gradient vanishing

> refers to the situation where the gradient of the loss function with  respect to the weights of the network becomes very small, making it  difficult for the network to learn. This can happen when the network has many layers, and the activations of the neurons in the **earlier** layers are **close to zero**, resulting in very small gradients during  backpropagation.

Gradient exploding

> refers to the situation where the gradient of the loss function with  respect to the weights of the network becomes very large, making it  difficult for the network to learn. This can happen when the network has many layers, and the activations of the neurons in the **earlier** layers are very **large**, resulting in very large gradients during  backpropagation.

How to solve these?

> One approach is to use more **advanced optimization algorithms**, such as  RMSProp or Adam, which can help to prevent the gradients from becoming  too small or too large.
>
> Another approach is to use **weight initialization** techniques, which can help to ensure that the weights of the network are initialized in a  way that avoids the vanishing or exploding gradients problem.
>
> Additionally, using techniques such as **batch normalization** or gradient  clipping can also help to prevent the gradients from becoming too small  or too large.

# Overfitting/Underfitting

Overfitting

> Overfitting refers to a situation in which a model performs well on the training data, but poorly on new or unseen data.
>
> when a model is **too complex** and has too many parameters, causing it to  memorize the details of the training data rather than learning the  underlying patterns. 

Underfitting

> refers to a situation in which a model performs poorly on both the training data and new data. when a model is **too simple** and does not have enough parameters to capture the complexity of the data.

Data augmentation (数据扩充) + Regularization (L1, L2), drop-out

## L1 L2 Penalty

Through the restriction of weight, make model disabled to learn stochastic noise in training data.

L1 Penalty make params more sparse, like function of feature capture. can't be derived.

$\min\frac{1}{2m}\sum_i(h_w(x^{(i)})-y^{(i)})^2+\lambda\sum_j|w_j|$

L2 Penalty doesn't. easy to be derived.

$\min\frac{1}{2m}\sum_i(h_w(x^{(i)})-y^{(i)})^2+\lambda\sum_jw_j^2$

# Activation Functions

Sigmoid $\sigma(x)=\frac{1}{1+e^{-x}}$

tanh(x)

ReLU $\max(0,x)$

Leaky ReLu $\max(0.1x,x)$

Maxout $\max(w_1^Tx+b_1,w_2^Tx+b_2)$

ELU $x(x\geq 0)$,$\alpha(e^x-1)(x<0)$

Why non-linear activate function?

> If linear, each layer input is linear combination output of last layer. The output of network is linear combination of input. no hidden layer, that is Perceptron(感知机).

What's the advantages of ReLU?

1. sigmoid function contains power which calls for more calculation especially when derivations. It's easy to differentiate ReLU.
2. for deep network, sigmoid in BP, exists gradient vanishing(when sigmoid is close to saturation region).
3. ReLU makes some neurons output 0, sparse network, easing over-fitting problem.

# Several Network

## CNN series

aka convolutional neural networks

receptive field 感受野

- local connection
- shared weight
- subsampling in space/time

conv2d: $y_{i,j}=\sum_u\sum_vw_{u,v}x_{i-u+1,j-v+1}$

z.B. LeNet, AlexNet, Inception

### ResNet

aka residual network, improve efficiency of info transmission.

$h(x)=x+(h(x)-x)$

$H(x)=h(x)-x$ is residual function.

take $H(x)$ as target function.

Residual unit

## RNN series

aka recurrent neural network, want to enable network to memorize.

turing-complete

$h_t=f(h_{t-1},x_i)$

neurons with self-feedback, applied in sequence processing(language, voice, music...)

RNN has long-range dependency problem due to gradient vanishing/exploding.

- solution: LSTM

### LSTM

aka long short-term memory, [detail](https://en.wikipedia.org/wiki/Long_short-term_memory)

input gate, output gate and a forget gate.

### Application of RNN series

language model: writing poems & lyrics, translation, seq2seq, chat bot (chatGPT)

## Generative Models

VAE(variational autoencoder), GAN(generative adversarial network)

### GAN

Generator network: input is stochastically sampling on latent space. trying to mimic true sample in training set, deceive the judger network.

Judger network: input is from true training set and generator network. trying to decide which input is true data.

The training process of G and J is a MiniMax Game. zero-sum

Hard to train!

z.B. DCGAN, Conditional GAN, seqGAN

