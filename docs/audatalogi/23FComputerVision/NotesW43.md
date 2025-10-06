---
title: 计算机视觉 W43 CNN architectures
author: tinsir888
date: 2023/10/23
cover: /img/AUcv.jpg
katex: true
tags:
  - 笔记
  - 计算机视觉
categories:
  - AUDatalogi
  - 计算机视觉
abbrlink: 7a6ff316
---

This class will introduce some typical CNNs.

# Transfer learning

pre-train finetune

replace decoder and train it.

time saving

## Examples

reinitialize and train only the last few fully connected layer.

encoder: more generic features (edge, blob, texture)

decoder: more specific features (object parties)

transfer learning freeze the encoder, whereas fine tuning unfreeze encoder.

# AlexNet

1998, first CNN.

conv filter 5*5, stride 1

subsampling layers were 2*2 applied at stride 2.

architecture: conv-pool-conv-pool-fc-fc

## Number of parameters

4 bytes per parameter

294 MiB

## Details

- first use of ReLU
- local response normalization layers
- heavy data augmentation
- dropout 0.5 in FC layers
- batch size 128, SGD Momentum 0.9, learning rate 1e-2.
- L2 weight decay 5e-4

# VGGNet

small filters, depper networks

from 8 layers to 16-19 layers.

only 3\*3 conv stride, pad 1 and 2\*2 max pooling stride 2.

Why use smaller filters?

> Stack of three 3\*3 conv stride layers has same effective receptive filed as one 7\*7 conv layer
>
> large size filters not necessary.

Number of parameters of one 7\*7 filter vs three 3\*3 filters:

> assuming 3 channel
>
> 1\*7\*7\*3=147
>
> 3\*3\*3\*3=81
>
> fewer parameters
>
> deeper, more non-linearities.

## Memory and number of parameters

total memory: 24M\*4 bytes=96MB\image

total params: 138M parameters, 552 MiB.

## Details

non-local response normalization

# GoogleLeNet

aka Inception V1

- architecture different from VGG, AlexNet
- 1\*1 conv at the middle of the network
- global average pooling at the end of the network instead of FC layers
- inception module: different size/types of conv for the same input and stacking all the output.

## 1\*1 convolution

for introducing more non-linearity to increase the representational power.

used as a dimension reduction module to reduce the computation.

by reducing computation bottleneck, depth and width of network can be increased.

## Inception module

design a good local network topology and then stack these modules on top of each other.

different kinds of features are extracted.

high computational complexity

## Global average pooling

FC layers are used at the end of network

all inputs are connected to each output

global average pooling is used nearly at the end of network by averaging each feature map from 7\*7 to 1\*1

number of weight=0

average pooling improved top-1 acc, less prone to overfitting.

## Auxiliary classifier

辅助分类器

intermediate softmax branches

used for training only

branch consist of:

- 5\*5 average pooling stride 3
- 1\/1 conv
- 1024 FC
- 1000 FC
- softmax

can be used for combating gradient vanishing problem, providing regularization

## Details

deeper network, with computational efficiency

22 layers

inception module

avoids expensive FC layers

only 5M params

# ResNet

deep residual networks

very deep networks using residual connections

152-layers model for ImageNet

motivation for going deeper: deeper, more model capacity

core idea: skip connection. skip one or more layers as shown in figure.

residual mapping

## Residual block

residual function $F(x)=h_W(x)-x$

easily push the residuals to 0s

## skip connection and shape

跳跃连接有两类：一类是直接跳，一类是跳的时候经过新的 conv 层

## training in practise

- Batch Norm after every conv layer
- Xavier 2/ initialization from He et al.
- SGD + Momentum 0.9
- learning rate 0.1, divided by 10 when validation error plateaus.
- mini-batch size 256
- weight decay 1e-5
- no dropout

# MobileNet

## Basic idea

- depthwise separable convolution is used to reduce the model size and complexity
- MobileNet is particularly useful for mobile and embedded vision applications
- smaller model size: fewer number of parameters
- lower complexity: fewer multiplications and additions

## Depthwise separable conv

depthwise conv is the channel wise $D_k\times D_k$ spatial conv.

pointwise conv is a 1\*1 conv to change the number of channels.

## Network architectures

Batch Norm and ReLU applied after each conv

## summary

separable conv replace standard conv by factorizing them into a depthwise conv and 1\*1 conv

much more efficient with little loss in accuracy

## MobileNet V2

add ResNet blocks

# Other types of architectures

## Autoencoder

无监督学习

decoder 重建图像， 学习 latent space representation

convolutional autoencoder

- encoder + decoder
- transposed convolution

## U-Net

loose fine image details at the bottleneck

skip connections: use the feature maps of the encoder to expand the bottleneck

designed for image segmentation

## Siamese networks

一个预训练模型，可以对接多个 downstream task model

## summary

ResNet and MobileNet are currently good defaults to use

Network have gotten increasingly deep over time

many other aspects of network architectures are also continuously being investigated and improved

more recent trend: meta-learning...

