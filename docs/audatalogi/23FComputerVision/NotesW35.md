---
title: 计算机视觉 W35 Introduction
author: tinsir888
date: 2023/8/28
cover: /img/AUcv.jpg
katex: true
tags:
  - 笔记
  - 计算机视觉
categories:
  - AUDatalogi
  - 计算机视觉
abbrlink: d7a12b7a
---

# 课程简介

用于视觉识别的深度学习 aka Deep Learning for Visual Recognition

Week 35 - Week 49，共 14 周（除去秋假一周）



第一周（Week 35）内容：Introduction

# Practical information

理论课：自底向上（理论优先）

实验课：编程联系，自顶向下（实践优先）

考试：15 min，5 topics，8 min pre + 4 min Q&A，占成绩的 50%。Project 占成绩的 50%。

# Introduction to traditional computer vision

a interdisciplinary field

从高级到低级：图像理解（物体检测、识别、形状分析）、图像分析（分割、匹配）、图像处理（增强、去噪、特征识别、压缩……）

image classification

semantic gap, what computer sees is a big grid of numbers

从猫的视觉神经中获得启发：特定的神经细胞对横线敏感

早期的计算机视觉模型

- 原始图像 - 差分 - 特征点选择
- all steps are hard-coded

- 早期方法的挑战
  - Illumination conditions（光照）
  - viewpoint variation（观察点从不同的方向看有变化）
  - scale variation（根据透视原理“近大远小”可能会造成错觉）
  - deformation（对象可能会出现奇怪的姿势）
  - occlusion（遮挡）
  - backgroud clutter（背景杂乱，对象和背景融为一体）
  - intra-class variation（某种类的物品的样式太多）

data-driven approach

- training dataset

- using traditional machine learning
  - **输入 - 特征提取 - 分类器 - 输出**
  - 特征提取：extract simple features like edges and blobs from input
  - 将特征输入机器学习的分类模型中
  - 训练模型并完成任务
- image features
  - edges 图像上强度、颜色、质地明显变化的分界线
  - more advanced structures 如 edge feature, line feature, four-rectangle feature 通过滤波器实现
  - interest points and decriptors (SIFT) 平移尺度不变特征
  - Histograms of oriented gradients (HoG) 定向梯度
  - super pixels 超级像素，将颜色近似的像素点替换成同一种像素点
- object detection and recognition
  - bag of words
  - part-based model

# Introduction to deep learning

deep learning ∈ machine learning ∈ artificial intelligence

机器学习

- 有一组输入 x 和输出 y，求输入输出对应的函数

- 作为“万能函数”，神经网络可以近似任何一种函数

深度学习

一种机器学习算法，inspired by the structure and function of the brain

big data + GPU

CNN 卷积神经网络

AlexNet 引领了深度学习

- conv1 提取 edge + blob

  用 96 种卷积核卷积得到 96 个二维特征图片

- conv3 提取 texture

- conv 5 object parts

- fc8 object classes

为什么深度学习如此强大？

> 传统机器学习的 feature extraction 需要的 expert-written computer program：
>
> - 需要专家
> - 编写耗时
> - 容易出错
> - 不容易泛化到新问题
>
> 而深度学习不需要专门的 feature extraction，这一步直接交给网络模型做

learning principle

每一张图片算一个输入 x，对应的输出 y

目标是：

- learn a model that is good at predicting y from x
- good generalizes to unseen data

计算 error，通过 gradient descent 求最小 error 值

评估模型：欠拟合、拟合、过拟合

CNN 的卷积层

卷积→激活函数（非线性）→池化（减小特征图尺寸同时保留重要特征。减少计算负担、特征不变性、降低过拟合）

CNN 也可以从 encoder/decoder 的视角看待：

> 原始输入图片经过多层的卷积层得到特征图（encode）
>
> 特征图经过全连接层得到输出（decode）

CV 的任务

- 图像分类
- 图像分类+位置识别
- 物品识别
- 图像分割 segmentation

CV 的一些应用

- 灰度图上色
- fake face
- 图像风格迁移
- 根据图像生成文字
- 检测人体动作

目前的 SOTA 模型

- DALL-E 2
- Stable Diffusion 稳定扩散

# Lecture plan for the rest

W2 基础机器学习（训练、验证和测试）

W3+4 传统神经网络（反向传播）

W5 CNN&如何写好报告

W6+7 训练 CNN

W8 CNN 架构

W9 目标检测和分割

W10 生成式模型

W11 可视化、理解 CNN

W12 Trending topics

W13 NLP 自然语言处理

W14 info about exam