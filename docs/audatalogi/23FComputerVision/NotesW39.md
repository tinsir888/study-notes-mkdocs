---
title: 计算机视觉 W39 Backpropagation
author: tinsir888
date: 2023/9/25
cover: /img/AUcv.jpg
katex: true
tags:
  - 笔记
  - 计算机视觉
categories:
  - AUDatalogi
  - 计算机视觉
abbrlink: 9083d0c1
---

# Back propagation with manual derivation

Let's consider a neural network

$L$ is total number of layers

$s_l$ is number of units (except bais) in layer $l$.

$(W,b)=(W_{ij}^{(l)},b_i^{(l)})_{l=1}^{n_L}$ is model parameters, where we write $W_{ij}^{(l)}$ to denote the weight associated with the connection between unit $j$ in layer $l$, unit $i$ in layer $l+1$. $b_i^{(l)}$ is the bias associated with unit $i$ in layer $l+1$. $W^{(l)}$ has size $s_{l+1}\times s_l$ and $b^{(l)}$ has length $s_{l+1}$.

## Forward propagation

$a^{(i)}=\sigma(z^{(i)})$

$z^{(i)}=W^{(i-1)}a^{(i-1)}+b^{(i-1)}$

goal of training: learn parameters s. t. $h_{Wb}(x^{(k)})=y^{(k)}$

## Back propagation

intuition $\delta_j^{(l)}$ = error of unit $j$ in layer $l$.

$\delta^{(4)}=(a^{(4)}-y^{(k)})\odot\sigma'(z^{(4)})$

$\delta^{(i)}=(W^{(i)})^T\delta^{(i+1)}\odot\sigma'(z^{(i)})$

$\frac{\partial J(W,b)}{\partial W_{ij}^{(l)}}=a_j^{(l)}\delta_i^{(l+1)}$

$\frac{\partial J(W,b)}{\partial b_i^{(l)}}=\delta_i^{(l+1)}$

## Proof

### last layer

using chain rule!

$\delta_j^{(L)}=(a_j^{(L)}-y_j)\sigma'(z_j^{(L)})$

$J(W,b)=\frac{1}{2}\sum_k(h_{W,b}(x)_k-y_k)^2=\frac{1}{2}\sum_k(a_k^{(L)}-y_k)^2$

derivative r.w.t. $a_j^{(L)}$: $\frac{\partial J(W,b)}{\partial a_j^{(L)}}=a_j^{(L)}-y_j$

define error of unit j in layer l is: 
$$
\delta_j^{(l)}=\frac{\partial J(W,b)}{\partial z_j^{(l)}}\\
=\sum_k\frac{\part J}{\part a_j^{(l)}}\times\frac{\partial a_j^{(l)}}{\partial z_j^{(l)}}\\
=(a_j^{l}-y_j)\sigma'(z_j^{(l)})
$$
*proof of intermediate layers is the same*

### Weight update

$\frac{\partial J(W,b)}{\partial W_{ij}^{(l)}}=a_j^{(l)}\delta_i^{(l+1)}$

$\frac{\partial J}{\partial W}=\frac{\partial J}{\partial z}\times\frac{\partial z}{\partial W}$

### Bias update

$\frac{\partial J(W,b)}{\partial b_{i}^{(l)}}=\delta_i^{(l+1)}$

$\frac{\partial J}{\partial b}=\frac{\partial J}{\partial z}\times\frac{\partial z}{\partial b}$

# Training NNs in practice

## Saturation

aka 饱和

以 sigmoid 函数为例，当 输入比较大时，激活函数的函数值改变很小。因此计算的值就会很小，网络更新缓慢。

选择非 sigmoid 函数解决这个问题。

leaky ReLU, Maxout, ELU, ReLU, tanh......

## Vanishing gradient problem

饱和会导致梯度消失

gradient of the loss function can approach zero. 导致传播时，梯度越传越小，更新不动网路参数。

possible solutions: batch normalization and residual network 批次正则化，残差连接

## Over-fitting

too many parameters, too few training data.

fail to generalize to new data.

### L2 regualrization

weight decay

$J(W,b)=\frac{1}{2}\frac{1}{n}\sum_i\sum_k(h(x^{(i)}_k)-y_k^{(i)})+\frac{\lambda}{2n}\sum_l\sum_j\sum_i(W_{ij}^{(l)})$

the partial derivative gets an extra term:

$\frac{\partial J(W,b)}{\partial W_{ij}^{(l)}}=\frac{1}{n}\Delta W_{ij}^{(l)}+\lambda W_{ij}^{(l)}$

### other types of regularization

dropout

early stopping

- up to a certain point, gradient descent improves the models performance on data outside of the training set
- provide guidance as to how many iterations can be run before the model begins to overfit.

batch normalization

data augmentation

stochastic gradient descent

- for large training set, computing loss and gradient for entire training set is very slow.
- generally each parameter update in SGD is computed w.r.t. a few training examples or a minibatch as opposed to a single example.

using other activation function

### detecting over-fitting

if the loss function on training set always decrease over time, showing no tendency to convergence.

meanwhile, loss on validation set increases.

