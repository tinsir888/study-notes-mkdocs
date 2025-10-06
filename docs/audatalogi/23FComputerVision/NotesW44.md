---
title: 计算机视觉 W44 Object detection and segmentation
author: tinsir888
date: 2023/10/30
cover: /img/AUcv.jpg
katex: true
tags:
  - 笔记
  - 计算机视觉
categories:
  - AUDatalogi
  - 计算机视觉
abbrlink: 4802d8c1
---

# Object localization

## Classification + Localization: ImageNet

1000 classes

each image has 1 class and at least one bounding box

800 training images per class

in the competition, algorithm should produce 5 (class, box) predictions

correct if at least one prediction has the correct class and a bounding box with at least 0.5 intersection over union IoU

## Evaluation

mean average precision mAP

compute average precision separately for each class, then average over classes.

a detection is a true positive if it has IoU with the ground-truth box greater than some threshold (usually mAP at 0.5)

combine all detections from all test images to draw a precision/recall curve for each class; AP is area under the curve

TL; DR mAP is a number from 0 to 100; higher, better.2
$$
IoU(A,B)=\frac{|A\cap B|}{|A\cup B|}\\
\text{precision}=\frac{\text{number of correct detections}}{\text{total detections}}\\
\text{recall}=\frac{\text{number of ground truth with matched detections}}{\text{total ground truth}}
$$
classification:

- C classes
- input: image
- output: class label
- evaluation metric: accuracy

localization: (regression)

- input image
- output: box in the image
- evaluation metric: intersection over union



step 1: download a pre-trained CNN classifier

step 2: attach new fully-connected regression head to the network

step 3: train the regression head (use L2 loss)

step 4: at test time use both heads

## Sliding window

### Motivation

problem: what if we want to localize an arbitrary number of objects?

solution: run classification at multiple location on a high-resolution image.

overfeat:

- first step towards detecting multiple objects
- run classification + regression network at multiple locations on a high-resolution image
- convert fully-connected layers into 1\*1 convolutional layers for efficient computation
- combine classifier and regressor predictions across all scales for final prediction

### Problem

#### #1 Speed

using a standard CNN classifier for each window position is **slow**!!!

solution: convert fully connected layers into convolutional layers for efficient computation

这里稍微听一下细节

#### #2 Scale/size

window size is fixed, can only detect objects of fixed size

windows are square, but not all objects are square

solution: overfeat.

- add localization: predict both class labels and bounding box at each window position.

这里稍微听一下细节

aside: non-max suppression

- simple heuristic procedure that preserves only the best bounding boxes.
- go down the list of detections starting from highest scoring
- eliminate any detection that overlaps highly with a higher scoring detection

in practice use many sliding window locations and multiple scales.

# Object detection

## Object detection using sliding window

Problem: need to test many position and scales and use a computationally demanding classifier.

solution: only look at a tiny subset of possible positions.

Region proposal with selective search

- basic idea: find image regions that are likely to contain objects and run classifier only on those regions.
- much faster than sliding window

- based on traditional, cluster-based image segmentation

- many different segmentation algorithms (k-means...)

  bottom-up segmentation, merging regions at multiple scales (super pixels)

## R-CNN

aka Regions with CNN features

we can leverage any image classification approach

For each region proposal run through CNN and extract 4096 dimensional feature vector from last fully connected layer

use support vector machine classifier of pre-computed feature vectors

### Steps

1. detect 2000 regions of interest (RoI) using selective search
2. candidate regions mush be wraped to input shape that AlexNet expects
3. get 4096 dimensional feature vector from modified AlexNet that recognizes 20 classes + background
4. classify using SVM and predict bounding box coordinates using regression for more precise localization

### Problems

#1: slow at test-time

#2: SVM and regressors are post-hoc: CNN features not updated/trained in response to SVMs and regressors.

#3: complex multistage training pipeline

#4: selective search algorithm is a fixed algorithm

## Fast R-CNN

solution for problem #1: share computation of convolutional layers between proposals for an image.

solution for problem #2 and #3: just train the whole system end-to-end all at once.

### RoI pooling

high-resolution image with region proposal -- conv and pooling -- hi-res conv features with region proposal 

probelm: fully-connected layers expect low-res conv features

solution: divide projected region in hi-res features into $h\times w$ grids. max-pool within each grid cell.

can back propagate similar to max-pooling

Fast R-CNN is trained end-to-end. This figure shows only the classifier pipeline. There is a similar pipeline for the bounding box regressor.

### Problem

region proposal slows down the algorithm significantly when compared to not using region proposals.

region proposal: bottleneck

solution: make CNN do region proposal.

## Faster R-CNN

insert a region proposal network RPN after the last conv layer

RPN trained to produce region proposal directly, no need for external region proposals

after RPN use RoI pooling and an upstream classifier and bounding box regressor just like Fast R-CNN

### Region Proposal Network

- slide a small window over the feature map
- build a small network for
  - classifying object or not-object and regressing bbox location
  - regressing bbox location
- position of sliding window provides localization info with reference to the image
- box regression provides finer localization information with reference to this sliding window

- at each location, consider boxes of many different sizes and aspect ratios
- use n anchor boxes at each location
- anchors are translation invariant: use the same ones at every location
- regression gives offsets from anchor boxes
- classification gives the probability that each anchor shows an object

### Results and comments

use alternating optimization to train RPN, then Fast R-CNNN with RPN proposals

more complex than it needs to be

4 losses

- RPN classification (anchor good/bad)
- RPN regression (anchor -> proposal)
- Fast R-CNN classification (over classes)
- Fast R-CNN regression (proposal -> box)

# One-stage object detection

R-CNN series is a 2-stage object detector

first stage: run once per image

- shared conv layer network
- region proposal network

second stage: run once per region

- corp features: RoI pool/align
- predict object class
- prediction bbox offset

## YOLO

aka you only look once

idea: no region proposals

a single regression problem, straight from image pixels to bounding box coordinates and class probabilities

- fast 
- reason globally
- learn generalizable representations

## YOLO details

1. divide image into 7\*7 cells
2. for each cell predict bbox coordinates and confidence scores
3. for each cell, predict the class probabilities.
4. final output - pick cells with confidence above threshold and non-max suppression to prevent overlapping bboxes.

## SSD: Single Shot Detector

similar to YOLO, but denser grid map. multi scale grid maps + data augmentation + hard negative mining + other design choices in the network

## RetinaNet

extreme foreground-background class imbalance problem in one-stage detector training

use a special **focal loss** 焦点损失 to lower contribution from easy negative samples so that the loss focusing on hard samples, which improves the prediction accuracy.

# Semantic segmentation

label each pixel in the image with a category label

only care about pixels

our goal is to take either a RGB color image or a gray-scale image and output a segmentation map where each pixel contains a class label represented as an integer.

one-hot encoding: class labels

depth-wise argmax

- our model should predict a vector of class probabilities for each pixel
- the prediction can be collapsed into a segmentation map by taking the depth-wise argmax of each pixel vector

## FCN

aka fully convolutional network

take a CNN classifier and make it fully convolutional

- variable size input shape
- variable output shape

output is a volume of heatmaps of class probabilities

problem with simple FCN

- the output is the same size as the final feature map, so we need up-sampling

### Methods for up-sampling

nearest neighbor

bed of nails

max pooling - max up=pooling

Learnable up-sampling

- transpose convolution
- Transpose convolution instead takes a single value from the low-resolution feature map and multiply all of the weights in our filter by this value, projecting those weighted values into the output feature map.

problem for up-sampling: 模糊了形状 need fine details!

idea: skip layers. jointly learn to combine outputs from multiple layers

## U-Net

improves upon the FCN through **expanding the capacity of the decoder** module of the network

symmetric expanding path.

## Instance segmentation

problem: semantic segmentation doesn't highlight individual instances of a class differently

we can't distinguish one cow from another

### Mask R-CNN

mask R-CNN segments each instance of a class in a image in two steps

- perform object detection to draw bounding boxes around each instance of a class
- perform semantic segmentation on each of the bounding boxes