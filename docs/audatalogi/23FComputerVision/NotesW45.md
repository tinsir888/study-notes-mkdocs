---
title: 计算机视觉 W45 Generative models
author: tinsir888
date: 2023/11/6
cover: /img/AUcv.jpg
katex: true
tags:
  - 笔记
  - 计算机视觉
categories:
  - AUDatalogi
  - 计算机视觉
abbrlink: 56ab7808
---

# Unsupervised learning

regression vs. classification

why unsupervised learning?

- few of data are labeled
- pre-train an unsupervised model
- anomaly detection - learn what data distribution typically looks like
- cheaper than supervised learning

## Generative models

a generative model is a class of statistical model that contrasts with discriminative models.

informally

- discriminative model - classifier $\text{data}\sim p_{data}(x)$
- generative model - generate new data instances $\text{generated samples}\sim p_{model}(x)$

generative model includes the distribution of the data itself and tells you **how likely a given example is**.

for example, models that **predict the next word in a sequence** are typically generative models.

many kinds

## Generative models are hard

- have to model more - generative model try to model how data is placed throughout the space
- a generative model for images might capture some **difficult correlations**.

# Autoencoder

## Traditional Autoencoder (AE)

an AE is a neural network that is trained to attempt to copy its input to its output

given data $x$ (no labels) we would like to learn function $f$ (encoder) and $g$ (decoder) s.t.:
$$
h=f(x)\\
\hat x=g(h)\\
\hat x=g(f(x))=x
$$
where g is an **approximation** of the identify function

$h=f(x)=s(Wx+b)$ is the **latent** or **hidden representation** or **code** and $s$ is a non-linearity function.

$\hat x=g(h)=s(W^Th+b')$ is $x$'s **reconstruction**.

### Motivation

- learning the identity function $g$ everywhere is not especially useful
- autoencoders are restricted in ways that allow them to copy only approximately.
- traditionally an autoencoder is used for dimensionality reduction and feature learning

### Training an encoder

training set is no-label.

using SGD

loss function: L2 Norm

### undercomplete vs overcomplete AE

AE is undercomplete if hidden representation has smaller dimensionality than the input

- good feature for the training distribution
- bad for other types of input

AE is overcomplete if hidden representation has larger dimensionality than the input

- no guarantee that the hidden units will extract meaningful structure
- higher* dimension code helps model a more complex distribution - good for training a linear classifier.

## Stacked AE - deep

deep is better

- more representational power
- easier for similar datapoints to group together.
- better separation of classes

## Latent space

the space in which the encoding exists is called the latent space or the hidden space

if AE is undercomplete, the compressed latent representation $h$ is often called the bottleneck

what if we tr to interpolate 插值 between two latent space? $h_i=\alpha h_1+(1-\alpha)h_2$

我们可以发现，$\alpha$ 从 0 到 1，得到的 $h_i$ 像 $h_1$ 代表的东西变得越来越像 $h_2$ 代表的东西了

## Problem with simple autoencoders

- latent space may not be continuous
- 容易变成 replicate

solution: variational autoencoders

## Regularization

motivation: learn meaningful features without altering the code's dimensions

solution: imposing other constraints on the network

## Sparse AE

suppose the AE has learned this hidden representation features.

It has just *remembered* the data, not *learned* it. This will not generalize well to unseen data

the image is decomposed into a combination of sparse features:

- if our learned features are sparse, we can generalize better
- sparse means: most activations are 0 except a few that are close to 1.

Let $h_j^{(l_{Bn})}$ denote the activation of $j$-th hidden unit of the autoencoder

Let $h_j^{(l_{Bn})}(x)$ be activation of this specific node on a give input $x$.

Let $\hat\rho_j=\frac{1}{n}\sum_{i=1}^n[h_j^{(l_{Bn})}(x^{i})]$ be the average activation of hidden unit $j$.

we would like to enforce the constraint $\hat\rho_j=\rho$ where $\rho$ is a *sparsity parameter*, typically small.

in other words, we want the average activation of each neuron $j$ to be close to $\rho$.

we penalize $\hat\rho_j$ for deviating from $\rho$

penalty term: $\sum_{j=1}^{S_{Bn}}KL(\rho|\hat\rho_j)$

$KL$ is a Kullback-Liebler divergence function, $S_{Bn}$ is the number of units in the hidden layer

$KL(\rho|\hat\rho_j)=0$ if $\hat\rho_j=\rho$

overall loss function: $J_S(W)=J(W)+\beta\sum_{j=1}^{S_{Bn}}KL(\rho|\hat\rho_j)$

$J(W)=\frac{1}{2}\sum_{i=1}^n||\hat x^{(i)}-x^{(i)}||$

we need to know  $\hat\rho_j$ before hand, so we have to compute a forward pass on the whole training set.

### Recap

cross-entropy: $H(p,q)=-\sum_{i=1}^np_i\log_2(q_i)$

KL divergence: $D_{KL}(p||q)=H(p,q)-H(p)=-\sum_{i=1}^np_i\log_2{q_i}-(-\sum_{i=1}^np_i\log_2p_i)=-\sum_{i=1}^np_i\log_2\frac{q_i}{p_i}$

## Denoising AE

aim to encode the input to learn and describe latent attributes of the data

try to undo the effect of a corruption process stochastically applied to the input

model isn't able to simply develop a mapping which memorizes the training data, becuase input and target output are not the same

更稳定 robust 的模型：在输入的时候加上噪声，目标输出还是原图

$\hat x=g(f(\widetilde x))=x$ where $\widetilde x$ is a copy of $x$ that has been corrupted by some noise process

### example of noise

random assignment of subset of inputs to 0 with probability $v$.

Gaussian additive noise.

### training

reconstruction $\hat x$ computed from the corrupted input $\widetilde x$

loss function compares $\hat x$ reconstruction with the noiseless $x$

what does it learn?

> the correlations of $x$'s features
>
> based on those relations we can learn a more "not prone to changes" model
>
> we are forcing the hidden layer to learn a generalized structure of the data

The DAE is trained to map a corrupted data point back to the manifold. DAE 经过训练，可以将损坏的数据点映射回流形。

### Going convolutional

we can replace the fully connected layers with convolutional layers

# Variational autoencoders

very similar to the regular autoencoder

unique property: latent space are continuous (by design), allowing random sampling and interpolation

how?

> by making its encoder not output an encoding vector of size $n$, but rather outputting two vectors of size $n$.
>
> a vector of means $\mu$, and another vector of standard deviations $\sigma$
>
> probabilistic nature using a sampling layer

## Random sampling layer

the stochastic generation means, that even for the same input, while the mean and standard deviations remain the same, the actual encoding will somewhat vary on every single pass simply due to sampling.

将输出整成了一个概率分布而不是一个确定的值。

- the hidden layer outputs the parameters of a vector of random variables of length $n$
- the $i$-th element of $\mu$ and $\sigma$ represents the mean and standard deviation of the $i$-th random variable $x_i$, from which we sample to obtain the sampled encoding, which we pass onward to the decoder

## Probabilistic nature of VAE

not only is a single point in latent space referring to a sample of that class, but all nearby points refer to the same as well

the decoder is exposed to a range of variations

results in a smooth latent space

Local smoothness is not enough

- if the encoder learns to cluster samples apart, the decoder will be able to reconstruct the training data better

- we want encodings all of which are as close as possible to each other while still being distinct, allowing smooth interpolation, and enabling the construction of new samples.

  最好这些 smooth 的类别分布能紧密挨着，分开了就不好

solution:

- KL divergence in the loss function
- for VAEs, the KL loss is equivalent to the sum of all the KL divergences between the latent component $Z_i\sim N(\mu_i,\sigma_i^2)$ in $Z$. and the standard normal, where $\mu_i=0,\sigma_i^2=1$
- KL loss term: $\sum_{i=1}^n\sigma_i^2+\mu_i^2-\log\sigma_i-1$
- we still minimize similarity as well $\frac{1}{2}\sum||\hat x-x||^2$

### Effect of KL loss term

encourages the encoder to distribute all encoding, evenly around the center of the latent space

Optimizing similarity and KL loss, results in the generation of a latent space which maintains the similarity of nearby encodings on the local scale via clustering, yet globally, is very densely packed near the latent space origin.

# Generative Adversarial Network (GAN)

## Density estimation

goal of probabilistic generative models: fund model s.t. $p_{data}(x)=p_{model}(x)$

explicitly model density function (distribution)

- 高斯分布、VAE 的 latent 计算

implicit: GAN - just learn to generate samples from distribution

## motivation of GAN

problem: want to sample from complex, high-dimensional training distribution. no direct way to do this

solution: sample from a simple distribution.



System of 2 neural network competing against each other in a 0-sum game framework

discriminator network: try to distinguish between real and fake images

generator network: try to fool the discriminator by generating real-looking images

## Training GAN

$D(x)$ represents the probability that input $x$ came from the real data rather than the generator

$G(z)$ takes random input noise vector $z$ and transforms it into an image

minibatch consists of 

- $\{x^{(i)}\}_{i=1}^m$ examples from the real distribution
- $\{z^{(i)}\}_{i=1}^m$ samples from a prior distribution (like Gaussian)

then, from the discriminator's perspective, we would like to have

$D(x^{(i)})=1$ (real), $D(G(z^{(i)}))=0$ (fake) $\Rightarrow\max_{W_D}\sum_{i=1}^m\log(D(x^{(i)}))+\log(1-D(G(z^{(i)})))$

but for generator's perspective, we would like to have

$D(G(z^{(i)}))=1$ (fool the discriminator) $\Rightarrow\min_{W_G}\sum_{i=1}^m\log(1-D(G(z^{(i)})))$

### Loss function

minimax game
$$
\min_{W_G}\max_{W_D}\mathbb E_{x\sim p_{data}}\log(D(x))+\mathbb E_{z\sim p_z}\log(1-D(G(z)))\\
=\int p_{data}(x)\log(D(x))\text dx+\int p_z\log(1-D(G(z)))\text dz
$$
for a fixed generator, it can be shown that this minimax game has a global optimum for $p_{data}(x)=p_{generator}(x)$

in that case, the discriminator can't distinguish the real from the fake, because
$$
D^*(x)=\Pr(real|x)=\frac{p_{data}(x)}{p_{data}(x)+p_{generator}(x)}=\frac{1}{2}
$$
it works!

- $G$ has a reinforcement learning task
  - it knows when it does good but it is not given a supervised signal
  - RL is hard
  - back propagation through $D$ provides $G$ with a supervised signal; the better $D$ is, the better this signal will be.
- Can't describe optimum via a single loss
- $D$ is seldom fooled

### Common problems in training GAN

hard to train!

- non-convergence: the model parameters oscillate, destabilize and never converge

- mode collapse: the generator collapses which produces limited varieties of samples

  表现：生成器往往只生成单一类型的数据

- diminished gradient: the discriminator gets to successful, so the generator gradient vanishes

- unbalanced between generator and discriminator causing overfitting

- highly sensitive to the hyperparameter selections

## Some variation of GAN

### Deep Convolutional GAN

aka DCGAN

加了卷积操作的对抗生成网络

### Conditional GAN

aka CGAN

the original GAN generates data from random noise, but has no knowledge about class labels

CGAN aims to solve this by telling the generator to generate images of only one particular class, like a cat or dog

specifically, CGAN concatenates a one-hot vector $y$ to the random noise vector $z$ to result in an architecture.



Pix2Pix



CycleGAN - cycle consistency loss



ProGAN - progressive growing of GAN



WGAN - wasserstein GAN



SAGAN - self attention GAN



BigGAN



# Summary

Auto encoders

- best for dimensionality reduction / feature learning.
- Not good at generating new samples due to discontinuities in latent space

variational autoencoders VAE

- useful latent representation. better at generating new samples, but current sample quality not the best.

generative adversarial networks GAN

- game-theoretic approach, best samples
- but can be tricky and unstable to train

