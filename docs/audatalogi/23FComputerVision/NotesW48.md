---
title: 计算机视觉 W48 Roundup - Uncovered Areas (新课完结)
author: tinsir888
date: 2023/11/27
cover: /img/AUcv.jpg
katex: true
tags:
  - 笔记
  - 计算机视觉
categories:
  - AUDatalogi
  - 计算机视觉
abbrlink: 48cec433
---

# Cross-entropy revisited

Binary cross-entropy loss

$$
J(w)=-\sum_{i=1}^n(y^{(i)}\log(h_w(x^{(i)}))+(1-y^{(i)})\log(1-h_w(x^{(i)})))\\
$$

$$
=-\sum_{i=1}^n\sum_{k=0}^1\mathbf 1\{y^{(i)}=k\}\log(\Pr(y^{(i)}=k|x^{(i)}))
$$

categorical cross entropy loss is used for multi-class classification. similar

Where does loss function actually come from?

> entropy, cross entropy and KL divergence

## Entropy

information entropy is a measure of the uncertainty associated with a given probability distribution $\Pr(x_i)$

formally defined as the average amount of information you get from one random sample, drawn from a probability distribution $p$

the concept is due to Claude Shannon and was introduced in 1948

the goal is to reliably send a message from a sender to a recipient

messages are composed of bits, but not all bits are useful: some are redundant, some are error etc.

so when we send a message, we want as much useful information as possible to get through

In Shannon's theory, sending one bit of information means to reduce the receiver's uncertainty by a factor of two.

幻灯片有一个以天气预报为例解释信息熵的例子

the probability distribution is:
$$
H(\mathbb p)=-\sum_{i=1}^np_i\log_2(p_i)
$$

## Cross-entropy

the cross entropy between two probability distributions $p$ and $q$ over the same underlying set of events measures **the average number of bits needed to identify an event drawn from the set** if a coding scheme used for the set is optimized for an estimated probability distribution $q$, rather than the true distribution $p$.

幻灯片有一个以天气预报为例解释交叉熵的例子

note that if the predicted probabilities equal the true probabilities, then the cross-entropy is just the entropy.

but if the two distribution differ, the cross-entropy will be larger than the entropy by some number of bits.

交叉熵实际上就是衡量两个我预测的概率分布和真实的概率分布差距的大小。
$$
H(\mathbb p,\mathbb q)=-\sum_{i=1}^np_i\log_2(q_i)
$$
$p_i$: true underlying distribution

$q_i$: predicted distribution

## Effect of KL loss term in VAEs

$$
D_{KL}(\mathbb p||\mathbb q)=H(\mathbb p,\mathbb q)-H(\mathbb p)
$$

KL 散度就是交叉熵减去信息熵。

the amount by which the cross-entropy exceeds the entropy, is called the relative entropy, or Kullback-Leibler Divergence.

the KL loss term pushes the latent representation towards a standard normal distribution, i.e., the i-th element is $Z_i\sim N(0,1)$.

## Application in softmax regression

we need a loss function that compares the predicted probabilities with the true probabilities.

use cross-entropy to compare them.

# Wrap-up on GANs

generator network: try to fool the discriminator by generating real-looking images

discriminator network: try to distinguish between real and fake images.

DCGAN = GAN + Deep Convolution

Conditional GAN

- the original GAN generates data from random noise but has no knowledge about class labels.
- CGAN aims to solve this issue by telling the generator to generate images of only one particular class, like a cat or a dog.
- specifically, CGAN concatenates a one-hot vector $\mathbb y$ to the random noise vector $\mathbb z$ to result in an architecture.

Pix2Pix

CycleGAN

ProGAN - aka progressive growing of GAN - purpose is to generate high resolution images

BigGAN

## Common problems in training GANs

non-convergence

mode collapse

diminished gradient

unbalanced between G and D

highly sensitive to hyper-parameter selections.

## Stabilizing GAN training

WGAN proposes a new loss function that has some nice properties that lead to a higher quality of the gradients to train the generator.

# Diffusion Models

## Motivation

GAN and VAE convert noise vector to image in a single forward pass.

fast but hard to learn a model

diffusion model generate image from noise by iterative denoising

slow but much easier to learn

## Recall Denoising Autoencoder

train an autoencoder to undo the effect of a corruption process stochastically applied to the input

the underlying idea is very simple: add random noise to the input and teach the auto encoder to remove the noise.

## Basic idea - 2 process

1. pre-defined forward diffusion: gradually adds noise to the input

   一共 t 步，每一步都加一点高斯噪声。有专门的公式去控制。

   diffusion kernel $q(X_t|X_0)=\mathcal N(x_t;\sqrt{\alpha_t}x_0,(1-\alpha_t)I)$

2. reverse denoising: neural network is trained to gradually denoise the data, starting from pure noise.

   neural network: approximate these conditional probabilities in order to run the reverse diffusion process.

（此处省略了一些有关 diffusion model 的细节）

## Conditional diffusion models

an additional input $y$ (class label or a text sequence) is available and we try to model the conditional distribution $p(x|y)$ instead.

in practice, the denoising model is also conditioned on $y$ in addition to the image from the previous timestep $x_t$.

this allows us to generate data given the conditioning signal.

## Classifier-guided diffusion model

main idea: train an extra classifier and mix its gradient with the diffusion model during sampling

## CLIP-guided diffusion model

given an image $x$ and prompt $y$, a CLIP model computes the alignment *cosine similarity* of x and y, which indicates how similar the image and the prompt are.

to use this signal for guidance, we assume that the CLIP similarity score is a good estimation of the function $p(y|x)$.

the gradient of this score w.r.t. the noised image, $x_t$ at timestep $t$ is used as the guidance gardient.

note that this requires the CLIP model to compute score for noised-images at intermediate timestep, hence a noised CLIP model is trained for guidance.

## DALL-E and Stable Diffusion

encoding from CLIP as the target encoding

用类似于 inverting CNN 的东西，找到相似 latent feature 表达的图。

DALL-E 2: conditioning on CLIP-embeddings

- helps capture multimodal representations
- the bi-partite latent enables several text-controlled image manipulation tasks

Stable Diffusion

- text embedding: describes the content in words
- random noise: random initialization, unique generated image every time

## Latent Diffusion Models

LDM applies the diffusion processes in the latent space instead of pixel space while incorporating semantic feed back from a transformer.

not only memory efficient, they also produce diverse, highly detailed images which preserve the semantic structure of the data.

## Conditioned Latent Diffusion

the image generation task is conditioned on a prior

denoising U-Net autoencoder with cross-attention (Q, K, V)

# Attention revisited

Self-attention in CNNs

Self-attention in GANs aka SAGAN

- add the attention weights back onto input layer itself with a weight of gamma, a learnable parameter initializing at 0. It means that the self-attention module does not do anything initially.

attention U-Net

- during unsampling in the decoder, spatial information recreated is imprecise. to counteract this problem, the U-Net uses skip-connections that utilize spatial information from the down-sampling in the encoder. Attention gates implemented at the skip connection will actively suppress activations in irrelevant regions, reducing the number of redundant features brought across.

CBAM aka convolutional block attention module

- attention module for CNN
- given an intermediate feature map, the module sequentially infers attention maps along two separate dimensions, channels and spatial, then the attention maps are multiplied to the input feature map for adaptive feature refinement.

# Self-supervised learning

goal: learn from data without manual label annotation

self-supervised learning methods solve pretext tasks that produce good features for downstream tasks

learn with supervised learning objectives e.g. classification, regression

labels of these pretext tasks are generated automatically.

## How to evaluate an SSL method?

we usually don't care about the performance of the self-supervised learning task e.g., we don't care if the model learns to predict image rotation perfectly.

evaluate the learned feature encoder on downstream target tasks.

### pretext task: predict rotations

self-supervised learning by rotating the entire input image

the model learns to predict which rotation is applied

### evaluation on semi-supervised learning

self-supervised learning on CIFAR10

freeze conv1 + conv2 learn conv3 + linear layers with subset of labeled CIFAR10 data.

### other pretext tasks

- solving jigsaw puzzles 拼图游戏
- predict relative path locations
- predict missing pixels
- image coloring

### Summary: pretext tasks from transformations

pretext tasks focus on visual common sense

the model are forced to learn good features about natural images e.g. semantic representation of an object category, in order to solve the pretext tasks

we don't care about the performance of these pretext tasks, but rather how useful the learned features are for downstream tasks.

problems:

- coming up with individual pretext tasks is tedious
- the learned representations may not be general

## A more general pretext task

contrastive representation learning

- given a chosen score function, we aim to learn an encoder network $f$ that yields high score for positive pairs and low score for negative pairs

Masked Autoencoder

（当时在华为实习的时候我就搞的这玩意……）

CLIP

# Siamese network

## motivation

assume that we want to build a face recognition system for a small organization with only 10 employees

using a traditional classification approach, we might come up with a system like: 输入图片 - 分类器 - 输出是每个种类对应概率值。

problem

1. to train such a system, we first require a lot of different images of each of the 10 person in the organization which might not be feasible.
2. what if a new person joins or leaves the organization? you need to take the pain of collecting data again and re-train the entire model again.

Siamese network helps to solve both of above issues, instead of directly classifying an input image to one of the 10 people in the organization, this network instead takes an extra reference image of the person as input and produces a similarity score denoting the chances that the two input images belong to the same person.

notice that this network is not learning to classify an image directly to any of the output classes, rather, it is learning a similarity function, which takes two images as input and expresses how similar they are.

in practice:

- to train this network, don't require too many instances of a class and only few are enough to build a good model.
- but the biggest advantage is that, let's say in case of face recognition, we have a new employee who has joined the organization. new in order for the network to detect his/her face, we only require a single image of his/her face which will be stored in the database. using this as the reference image, the network will calculate the similarity for any new instance presented to it. Thus, we say that network predicts the score in **one shot**.

## Architecture

将检测图和对比图输入同一个 ConvNet 中，分别得到两个图的 encoding，再将这两个 encoding 拿到判别其中，检查相似度，在经过 sigmoid 函数激活后得到相似度打分。