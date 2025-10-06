---
title: 计算机视觉 W41 Training ConvNet (2)
author: tinsir888
date: 2023/10/9
cover: /img/AUcv.jpg
katex: true
tags:
  - 笔记
  - 计算机视觉
categories:
  - AUDatalogi
  - 计算机视觉
abbrlink: 4d54f26b
---

# Training pipeline

set up architecture i.e. computational graph

train using mini-batch stochastic gradient descent (SGD)

loop:

1. sample a batch of images
2. forward propagation it through network, get loss
3. back propagation to calculate the gradient
4. update parameters using the gradient

## Be careful

activation function

- ReLU, careful with learning rates - leaky ReLU
- try tanh
- don't use sigmoid

data regularization

- always use zero-center data
- normalize to control variance
- apply the same pre-processing during training when using pre-trained network

weight initialization:

- depends on activation function

always use batch normalization

- FC - BatchNorm - ReLU

# SGD and momentum

## SGD

gradient descent: $w=w-\alpha\nabla J(w)$

batch gradient descent: compute loss w.r.t. parameters for the **entire** training set

SGD aka stochastic gradient descent: mini-batch, using a small, random set of n samples.

- n refers to batch size
- increasing n reduces variance of the parameter updates, more stable to convergence
- decreasing n creates random fluctuation to help avoid local minimal

### challenges

learning rate schedulers: try to adjust the learning rate during training but have to be defined in advance and are unable to adapt to a dataset's characteristics

SGD has trouble navigating ravines 穿越峡谷时震荡幅度大，容易跑偏。

## Momentum

momentum dampens oscillations by adding a fraction of the update vector of the past time step to the current update vector 动量抑制了震荡
$$
v_0=0\\
v_t=\gamma v_{t-1}+\nabla J(w)\\
w=w-\alpha v_t
$$
$\gamma$ is usually set to 0.9 or similar.

有了惯性/动量，模型就更容易收敛（类似于推一个球，能更快滚到山底的低点）

### Nestorov momentum

$$
v_0=0\\
v_t=\gamma v_{t-1}+\nabla J(w+\gamma v_{t-1})\\
w=w-\alpha v_t
$$

a smarter ball, which knows when to slow down before the hill slopes up again.

## In practice

always use momentum with SGD

setting the momentum term between 0.9 and 0.99

start with high learning rate and decay over time

# Learning rate decay and cycling

## Learning rate decay

intuition

- start with high learning rate to explore loss landscape
- slowly decay learning rate to tune in on a local minimum

tricky

### Step decay

reducing lr by half every 5 epochs, or by 0.1 every 20 epochs

### Other variants

linear decay

inverse sqrt decay

cosine decaay

## Cyclical learning rate

aka CLR

practically eliminates the need to experimentally find the best values and schedule for the global learning rates

let lr cyclically vary between reasonable boundary values

achieves improved classification accuracy

increasing the learning rate will force the model to jump to a different part of the weight space if the current area is spikey

## Linear warmup

some DNN suffer from a sort of early over-fitting

那么学习率从0开始在最初的几个 epochs 线性上升到最高点（这个过程被称为 warm-up），之后再下降

# Pre-parameter adaptive learning rate methods

## Motivation

tuning the learning rates is an expensive process, still require other hyper-parameter setting.

so there exist some adaptive methods

## Adagard

**smaller updates** for parameters associated with **frequently occurring** features, and **larger updates** for parameters associated with **infrequent** features.

improve the robustness of SGD

basic idea

- add element-wise scaling of the gradient based on the historical sum of squares in each dimension
- per-parameter learning rates or adaptive learning rates

$$
G_{0,i}=0\\
g_{t,i}=\nabla J(w_{t,i})\\
G_{t,i}=G_{t-1,i}+g_{t,i}^2\\
w_{t+1,i}=w_{t,i}-\frac{\alpha}{\sqrt{G_{t,i}+\epsilon}}g_{t,i}
$$



## RMSProp

adagard's weakness: accumulation of the squared gradients in the denominator

growing sum causes learning rate to shrink and eventually become infinitesimally small
$$
G_{0,i}=0\\
g_{t,i}=\nabla J(w_{t,i})\\
G_{t,i}=\gamma G_{t-1,i}+(1-\gamma)g_{t,i}^2\\
w_{t+1,i}=w_{t,i}-\frac{\alpha}{\sqrt{G_{t,i}+\epsilon}}g_{t,i}\\
\gamma\simeq0.9
$$

## Adam

in addition to storing an exponentially decay average of past squared gradients, adam also keeps an exponentially decaying average of past gradients, similar to momentum

## In practise

- adam is a good default choice
- SGD + Momentum can outperform adam but may require more tuning of learning rate and learning rate schedule

# Regularization

reduce over-fitting, to make model generalized to unseen data

## Early stopping

在模型快过拟合前，自动停止某些权重的更新

## Model ensembles

训练多个模型的副本，测试时取预测值的平均

## Dropout

in each forward pass, randomly set some neurons to zero.

hyper-parameter: probability p of dropping.

dropout prevents neurons from co-adapting too much, forces the network to learning a redundant representation, better at generalizing.

## Data augmentation

number of data is limited

create fake data and add it to the training set

how to create fake data?

1. 翻转图片
2. 旋转图片
3. 平移图片
4. 缩放图片
5. 添加噪点

## Noise robustness

training: add some kind of randomness

test: marginalize over the noise

cutout

- training: set random image region to zero

- test: use full image

  teach network to become invariant to occlusion

mixup

- training: train on random blends of images

- test: use original image

  encourages the model to behave linearly in-between training examples

label smoothing

- training: with slight noisy labels
- test: use original images

## Multi-task learning

hard parameter sharing: encoder shared between tasks

soft parameter sharing: distance between the parameters of the model is regularized in order to encourage the parameters to be similar

a model that learns 2 tasks simultaneously is able to learn a more general representation

averaging the noise patterns

## In practice

always use early stopping and batch normalization

regularization terms are often included by defualt

consider dropout for large fully-connected layers

data augmentation almost always a good idea

try cutout, mixup, label smoothing for small classification datasets

try multi-task learning if dataset has training data to solve many tasks

# Hyper-parameter tuning

how do we find optimal hyerparameters?

## General recommendation

use a single validation set of respectable size

search for hyper-parameter on log scale

prefer random search to grid search

careful with best values on borders

stage search from coarse to fine 搜索粒度由粗到细

start with 1 epoch or less, then add more epochs

# Babysitting the learning process

monitor during training

most important: **loss curves**

other useful things

- activation/gradient distribution per layer
- first-layer visualization

# Summary

- Activation functions: Use ReLU
- Data preprocessing: Subtract mean
- Weight initialization: Use Xavier/Kaiming
- batch normalization: Use always
- Optimizer: Use SGD+Momentum or Adam
- Regularization: Early stopping and data augmentation almost always a good idea
- Hyper-parameter search: Course-to-fine search and monitor loss curves