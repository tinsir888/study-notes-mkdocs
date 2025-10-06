---
title: 智能计算系统 第七章 深度学习处理器架构
author: tinsir888
date: 2022/4/13
cover: /img/aics.png
tags:
  - 笔记
  - 智能计算系统
categories:
  - 南开计科
  - 智能计算系统课程笔记
abbrlink: 6ac5cc3a
---
# 智能计算系统 第七章 深度学习处理器架构
## 7.1 单核深度学习处理器 DLP-S
- 控制模块
- 运算模块
- 存储单元
### 总体架构
从DLP到DLP-S
- 计算时
  - 多发射队列，支持指令级并行
  - 增加运算器的操作，支持硬件高效执行的操作
  - 低位宽运算器，提高执行能效
  - 稀疏运算，提高计算效率
- 访存
  - 转换检测缓冲器TLB，降低访存演出
  - 最后一级cache，降低访存延迟
### 执行流程
1. IFU 通过 DMA 从 DRAM 中读取程序指令，然后经过 IDU 进行译码后分发给 DMA、VFU 和 MFU
2. DMA 接收到指令后从 DRAM 读取神经元至 NRAM，读取权重至 WRAM
3. VFU 接收到指令后从 NRAM 中读取神经元数据，并对神经元数据进行预处理，然后发送给 MFU
4. MFU 接收到指令后从 VFU 接收经过预处理的神经元数据，从 WRAM 中读取权重数据，完成矩阵运算后将结果发送给 VFU
5. VFU 对输出神经元进行后处理（激活、池化等）
6. VFU 将运算结果写回 NRAM

神经元数据流
DRAM→NRAM→VFU→(MFU→VFU)→NRAM→DRAM
权重数据流
DRAM→WRAM→MFU
### 控制模块
IFU
- 地址生成器 AGU
- 指令高速缓存 ICache
- 指令回填单元RB
- 指令队列 IQ

IDU
- 译码单元
- 指令发射队列 IQ
- 算术逻辑单元
### 运算模块 VFU
- 完成输入神经元的前处理和输出神经单元的后处理
- 包括向量流水单元和转置单元
- 向量流水单元承载向量运算功能
- 转置单元承载数据重新摆放功能
### 向量流水单元
- 多种数据类型：INT8、INT16、INT32、FP16、FP32
- 新增运算：查表、边缘扩充、数据格式转换
- 多个 stage 输入，多个 stage 输出
### 向量流水单元如何完成 AVGPOOLINIG
当输入数据类型是 INT 型时
- AVGPooling 本质是kx*ky向量相乘
### 存储单元
存储管理
- NRAM、WRAM、DMA
- 虚拟存储：片内片外同一编址
- 片内地址无虚实地址转换
- 片外地址需要虚实转换
降低访问延迟
TLB、LLC
## 7.2 多核深度学习处理器 DLP-M
### 总体架构
多核处理器分层结构设计
- 一个 DLP-M 由多个 DLP-C 构成
- 一个 DLP-C 由多个 DLP-S 构成

DLP-C
- 四个 DLP-S
- 存储核 MEMCORE
  - 存储：DLP-S 共享数据
  - 通信：DLP-C 与片外 DRAM、DLP-C 之间，多个 DLP-S 之间
### Cluster 架构
广播总线
- 深度学习数据复用特性
- 读写请求：单播写、单播读、多播

CDMA
- 执行过程：单播写、CDMA 通信、单播读
- 访存指令：目标 Cluster号、源地址、目的地址、数据大小

多核同步模型
- BARRIER 指令：多核同步指令，解决访存冲突
  - BARRIER：Opcode
  - Barrier_ID：BARRIER 序号
  - Task_ID：同步的任务编号
  - Sync_Count：需要同步的 Barrier 数
- 双核协同指令流
### 互联架构
环形、网状、Torus