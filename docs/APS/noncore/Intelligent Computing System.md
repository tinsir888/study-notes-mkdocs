---
title: APS 非重点复习课程之智能计算系统
author: tinsir888
date: 2022/11/08
cover: /img/aps.png
katex: true
tags:
  - APS
  - 德国
  - APS 非重点复习课程
  - 智能计算系统
  - 留学
categories:
  - 留学申请
  - APS 审核复习
abbrlink: fac97af3
---

Intelligent Computing System is a combination of Deep Learning, Parallel Programming, Computer Organization and Computer Architecture.

# Neural Network Basis

Loss function $L(w)=\frac{1}{2}\sum_i(H(x_i)-y_i)^2=\frac{1}{2}\sum_i(w^Tx-y_i)$

Gradient Descent: $w=w-\alpha\frac{\partial L(w)}{\partial w}$

Activate Function

Back Propagation: Chain Rule $\frac{\partial L}{\partial w}=\frac{\partial W}{\partial y}\frac{\partial y}{\partial z}\frac{\partial z}{\partial w}$

Neural Network structure: input layer, latent layer, output layer

## CNN

convolution layer

pooling

fully connect + softmax $f(z_j)=\frac{e^{z_j}}{\sum_ie^{z_i}}$

z.B. alexnet, VGG, Inception, ResNet

How to judge CNN?

- IoU aka Jaccard index 交并比

  $IoU=\frac{A\bigcap B}{A\bigcup B}$

  if IoU>0.5, location accepted.

- mAP aka mean average precision

  mAP$=\frac{\sum_{q=1}^QAveP(q)}{Q}$

  recall$=\frac{TP}{TP+FN}$

  precision$=\frac{TP}{TP+FP}$

Object detective

R-CNN, YOLO

## RNN

sequence, recurrent, memory

LSTM

GRU

## GAN

generator, judger

CGAN, ConditionGAN

# Deep Learning Framework

## Tensorflow

Computation are expressed as stateful dataflow graphs.

All data is modelled as Tensor.

Computing operations running in Session.

Asynchronization execute stateful data flow graph through Queue.

Automatic differentiation

## PyTorch

- flexible
- Python and C++
- In research area

## MXNet

- R, Julia, Go
- Efficiency & flexibility

## Caffe

- The earlist
- lack flexibility
- No longer maintain

# Deep Learning Processor

aka deep learning accelerator

DLP is an electronic circuit designed for [deep learning](https://en.wikipedia.org/wiki/Deep_learning) algorithms, usually with separate [data memory](https://en.wikipedia.org/wiki/Computer_memory) and dedicated [instruction set architecture](https://en.wikipedia.org/wiki/Instruction_set_architecture).

Aim to optimize:

- Data level parallel
- Vectorize operation

DLP Instruction Set

Other accelerator

- GPU

- FPGA

# Deep Learning Language

Heterogeneous computing 异构计算

- Task division
- Data distribution
- Data communication
- Parallel and Synchronization

How to develop a new operator? 如何开发一个新算子

