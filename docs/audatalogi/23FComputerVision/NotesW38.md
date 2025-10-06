---
title: 计算机视觉 W38 Convolutional Neural Networks
author: tinsir888
date: 2023/9/18
cover: /img/AUcv.jpg
katex: true
tags:
  - 笔记
  - 计算机视觉
categories:
  - AUDatalogi
  - 计算机视觉
abbrlink: 732c15df
---

# Convolution

disadvantage of fully connected MLP: difficult to detect local features.

In fully connected neural network, we need a separate neuron to detect each of the items (same feature appear in different position). a waste of network parameters.

Solution: using convolution to identify local features

Advantages:

- fewer trainable params (sparse interactions)
- Same params reused multiple times (param sharing)
- translation invariance (equi-variance) 平移不变性

for continue:

$(f*g)(t)=\int_{-\infty}^\infty f(\tau)g(t-\tau)d\tau=\int_{-\infty}^\infty f(t-\tau)g(\tau)d\tau$

for discrete

$(f*g)(t)=\sum_{-\infty}^\infty f[k]g[t-k]=\sum_{-\infty}^\infty f[t-k]g[k]$

## Convolution in images

$g(x)$ is referred to as *filter* or *kernel*.

$(f*g)[x,y]=\sum_n\sum_mf[x-m,y-n]g[m,n]$

aka inner product between the filter **rotated by 180 degree** and the input.

## Example

高斯滤波：Gaussian filter acts as a smoothness filter. 使图像更平滑

索贝尔滤波：Sobel filters act as edge detectors. 边缘检测

## Conv in NN

output neuron is connected only to pixels in a certain region

all weights for neurons in the same layer are the same

motivation:

- sparse interactions
  - input img have many pixels, we detect small meaningful features with kernels that occupy only a few pixels.
  - reduce the mem requirements of model; improve statistical efficiency; requires fewer operations to compute the output
- parameter sharing
  - aka tied weight
  - weight applied to one input is tied to value of a weight applied elsewhere
  - learn a single set works for all locations
- equi-variant representation
  - euqivariance in translation: input changes some way, output changes the same way.

# Architecture of CNN

CNN are 3 dimensional height\*width\*channels

usually use local features, i. e. locally-connected

output is the dot product between filter w and small chunk x plus a bias $w^Tx+b$

connectivity:

- local in space
- full in depth (3 RGB channels)

Replication at the same area: usually we use many filters

we have n filters, the output at each location of the input image is 1\*1\*n tensor array.

think of output as a multi-dimensional image with n channels: each channel corresponds to a **feature map** or activation map.

note that the feature maps get smaller and smaller as we move deeper into the network.

**receptive field** 感受野

Param sharing

- CNN feature maps share the same weight/filter params

Stride 步长

output size: $\frac{N-W}{stride}+1$

zero-pad: add zeros at the border of the image

Pad $\frac{W-1}{2}$

## summary of conv layer

input $W_1\times H_1\times D_1$

num of filters $K$, spatial extent $F$, stride $S$, zero padding $P$

output $W_2\times H_2\times D_2$

$W_2=\frac{W_1-F+2P}{S}+1$

$H_2=\frac{W_2-F+2P}{S}+1$

$D_2=k$

## Activation layers

ReLU by default

why?

> trivial to calculate the derivative, speed up computation.
>
> avoid problems with small gradients, speed up the optimization.

## other layer types in CNN

### Pool layer

conv layers are often followed by pool layers

reduce the number of weights by keeping only the most important information.

max-pooling: choose the maximum

- adds some spatial invariance（空间不变性） to the exact feature locations

average-pooling: calc average

window size is the pooling range.

### fully connected layer

the same as layers in MLP

aka dense/linear layer

often used at the end of CNNs

output of conv is 3D tensor. reshape/flatten it to a vector as input of fully connected layer.

### 1*1 convolution

1\*1\*m convolution to mimic fully connected layers

# Example of CNNs

LeNet-5

- first CNN 1998

- conv filter 5\*5, stride = 1

- subsampling (pooling) layers were 2\*2 at stride 2

- architecture: conv - pool - conv - pool - fc - fc

AlexNet

VGG Net

## Training CNN

time-consuming

need large training data sets, avoid overfitting

A possible solution: transfer learning

- use pre-trained encode trained on ImageNet
- fine-tune weights of decoder
- training time: typically less than 1 hour

# Applications

Object classification and image retrieval

Object detection and segmentation

Human pose estimation

super resolution

# Summary

1. CNNs are based on sparse interaction and parameter sharing.
2. notice: training is time-consuming, prone to overfitting
3. transfer learning instead of training from scratch