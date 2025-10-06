---
title: 计算机视觉 W46 Visualizing and understanding CNNs
author: tinsir888
date: 2023/11/13
cover: /img/AUcv.jpg
katex: true
tags:
  - 笔记
  - 计算机视觉
categories:
  - AUDatalogi
  - 计算机视觉
abbrlink: 61b1055e
---

# Why visualizing

## The inner workings of ConvNets

After training, each layer progressively extracts higher and higher-level features of the image, until the final layer essentially makes a decision on what the image shows.

early layers: features like "corners" and "blobs"

late layers: semantic like "wheel" and "door". assemble features into complete interpretations

## Motivation

why visualize CNN?

> Debugging
>
> with neural networks we have little insight about learning and internal operation
>
> through visualization, we may:
>
> - observe how input stimuli excite the individual feature maps
> - observe the evolution of features during training
> - make better network designs
>
> as a bonus we can derive techniques for Neural Style Transfer and Texture Synthesis.

## Challenge

debugging deep neural network is tricky, because they are not invertible: many different inputs could have generated a particular output.

# Visualizing layer activations

the layer activation (aka feature map) is the output of the convolution operation

## Layer activations

the most straight-forward visualization technique is to show the activations of the network during the forward pass.

given an input image, plot what each filter has extracted after a convolution operation in each layer.

## Conclusions

ConvNets acts as information distillation 信息蒸馏 pipelines, where the input image is being converted to a domain, which is visually less interpretable but mathematically useful for the network to make a choice from the output classes in its last layer.

using layer activations to monitor training

- For ReLU networks, the activations usually start out looking relatively blobby and dense, butt as the training progresses the activations usually become more sparse and localized.
- one dangerous pitfall: easily noticed that with this visualization is that some activation maps may be all zero for many different inputs, which can indicate dead filters, and can be a symptom of high learning rates.

# Visualizing filters

convolution reflects the similarity between input and output

by visualizing filters/kernels we get an idea of what pattern each layer has learned to extract from the input.

## Example

typical-looking filters on the first convolution layer of a trained AlexNet

notice that the weights are very nice and smooth, indicating nicely converged network

Not converged network:

- working with image pixels it can be helpful and satisfying to plot the first-layer features visually.
- noisy feature could indicate
  - not converged network
  - improperly st learning rate
  - very low weight regularization penalty

滤波器（卷积核）的可视化图片通常来说越光滑越好，过多的噪点不好。

## Conclusions

the filter weight are useful to visualize because well-trained networks usually display nice and smooth filters without any noisy pattern

noisy patterns can be an indicator of a network that hasn't been trained for long enough. or possibly a very low regularization strength that may have led to overfitting

# Inspecting fully connected layers

the objective of fully connected layers is to take the results of the convolution/pooling process and use them to classify the image into a label

one-way to inspect an FC layer is as follows

- choose a layer. for AlexNet and VGG, FC layers output a 1000 or 4096 dimensional feature vector for each image.
- run the network on many images and collect the feature vectors
- run KNN
- for a given test image, compare with nearest neighbors. check similarity.

KNN finds images with similar feature representation

the similarities should be class-based and semantic, rather than pixel and color-based

# Feature visualization using dimensionality reduction

## Dimensionality reduction

another way to visualize learned feature representation (typically FC layers) is by reducing the dimensionality of feature vectors from high to 2 dimensions

### PCA

simple algorithm: PCA, aka principal component analysis

PCA is a technique for reducing the number of dimensions in a dataset whilst retaining most information

use correlation between some dimensions and tries to provide a minimum number of variables that keeps the maximum amount of variation or information about how the original data is distributed.

use eigenvalues and eigenvectors of the data's covariance matrix.

### t-SNE

aka t-distributed stochastic neighboring entities

another technique for dimensionality reduction and is particularly well suited for the visualization of high-dimensional dataset

contrary to PCA it's not an analytical technique but a probabilistic one.

observations that are close in the high-dimensional space will also be close in the low-dimensional space

t-SNE can help us understand what a particular CNN has learned

specifically, the t-SNE embedding gives us a rough idea of the topology of the learned representation

# Maximally activating patches

take a large dataset of images, feed them through the network and keep track of which images maximally activate some neuron.

we can then visualize the images to get an understanding of what the neuron is looking for in its receptive field.

## Receptive field

one of the most important concepts in CNN

all of the state-of-art object recognition methods design their model architectures around this idea.

definition: the receptive field is defined as the region in the input space that a particular CNN feature is looking at.

a receptive field of a feature can be described by its center location and its size.

## Conclusions

we can see from images that earlier layers learn more fundamental features, late layers learn more complex feautures

# Deconvolution

ZF Net

deconvolution was introduced in ZF Net to derive an improved AlexNet

the objective of this is to project hidden feature maps back into the original input space.

this allows us to visualize the activation functions of a specific filter

- deconvnet is attached to each of its layers
- input image is presented to the convnet
- to examine a given convnet activation, we set all other activations in the layer to zero and pass the feature maps as input to the attached deconvnet layer.

## Max unpooling

the max-pooling is non-invertible

switch variables - record the locations of maximal

it places the reconstructed features into the recorded locations

## Rectification

signals go through a ReLU operation to keep values positive

## Filtering

use of transposed conv

- Filters are flipped horizontally and vertically.
- Transposed convolution projects feature maps back to input space.
- Transposed convolution corresponds to the back-propagation of the gradient (an analogy from MLPs)

# Saliency maps

显着图

what is saliency: saliency maps tell us how different image regions affect the predicted class label.

## Saliency via occlusion

simple idea - mask part of the image before feeding to CNN, check how much predicted probabilities change.

we iterate over regions of the image, set a patch of the image to be all zero, and look at the probability of the class.

we can visualize the probability as a 2-dimensional heat map

# Reconstruction-based visualization techniques

## Inverting ConvNets

basic idea: generate an image $y$ from latent features

the input $x$ is the content image that we want to reconstruct

job is to find $y$ such is has latent features similar to those of $x$.

could use any pre-trained CNN

Let $a^{[l](C)}$ denote the output of layer $l$ of our CNN when $x$ is input.

Let $a^{[l](G)}$ denote the output of layer $l$ when $y$ is the input

job is to generate an image $y$ s.t. $a^{[l](G)}\approx a^{[l](C)}$

minimize: $J_C^{[l]}(x,y)=||a^{[l](G)}-a^{[l](C)}||_{\mathcal F}^2$ (Frobenius Norm: $||G||_{\mathcal F}=\sqrt{\sum_{ij}(g_{ij})^2}$)

we can regularize this optimization procedure using a norm regularizer.

and/or a total variation regularizer that encourages the image to be piece-wise constant:
$$
R_{V_\beta}(y)=\lambda_{V_\beta}\sum_{i,j,k}((y_{i,j+1,k}-y_{i,j,k})^2+(y_{i+1,j,k}-y_{i,j,k})^2)^{\frac{\beta}{2}}\\
\hat x=\arg_y\min J_C^{[l]}(x,y)+\lambda R(y)\\
$$

### Steps

steps for image reconstruction

1. initialize $y$ with random noise
2. feed forward pass the image
3. compute the loss function
4. compute gradients of the cost and backpropagate to input space
5. update generated image $y$ with a gradient step
6. repeat step 2 - 5 until convergence

### Conclusions

shed light on the information represented at each layer

more invariant and abstract notion of image content is formed in the network

## Reconstruction-based filter visualization

interpretations of VGG filter visualization

initial layer: simple directional edges and colors

deeper: more complex shapes

a lot of these filters are identical but rotated.

## Class-based reconstruction

start with an image full of random noise, then gradually tweak the image towards what the neural net considers a banana.

impose a prior constraint that the image should have similar statistics to natural images.

## Breaking CNNs with adversarial attacks

通过给图像加噪点，使得 CNN 判别错误

# Insights to generate new images

## DeepDream

ask network to enhance whatever it detected.

If we choose higher-level layers, which identify more sophisticated features in images, complex features or even whole objects tend to emerge.

## Texture synthesis

to generate high perceptual quality images that imitate a given texture.

## Neural style transfer

图像风格迁移

# Summary

to have a better understanding of ConvNets

visualization techniques can be used to

- observe how input stimuli excite the individual feature maps and class label predictions
- observe the evolution of features during training
- make better network designs