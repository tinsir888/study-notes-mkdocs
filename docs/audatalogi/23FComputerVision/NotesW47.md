---
title: 计算机视觉 W47 Sequence models (natural language processing)
author: tinsir888
date: 2023/11/20
cover: /img/AUcv.jpg
katex: true
tags:
  - 笔记
  - 计算机视觉
categories:
  - AUDatalogi
  - 计算机视觉
abbrlink: e7117333
---

# Sequence Models

- sequence models are the machine learning models that input or output sequences of data
- sequential data includes text streams, audio clips, video clips, time-series data etc.

# NLP

technology to handle human language using computers

aid human-human communication and/or human-machine communication

analyze/understand text (syntactic analysis, text classification, named entity recognition, building knowledge graphs).

## Language model ≈ next word prediction

$$
P(X=sentence)=\prod_{i=1}^tP(x_i|x_1,\cdots,x_{i-1})\\
P(Y=sentence|X=added\ context)=\prod_{i=1}^tP(y_i|X,y_1,\cdots,y_{i-1})\\
$$

## Word embeddings

words $x_i,y_i$ are represented as vectors, called word embeddings.

basic idea: words that appear in similar context should have similar embeddings

e.g. "good" and "great" should have similar embeddings

word embeddings are learned in many models, such as Word2Vec, GloVe, ELMo etc.

## Sentence embeddings

just like we can embed words, we can also embed complete sentences.

different approach because, unlike words, we can not define a finite set of sentence.

## Basic modeling paradigm

given an input text, extract features (embedding), and predict label.

text classification - one label for the whole sentence

sequence labeling - one label for every word

# Some sequence model

multi-layer preceptron will not work for sentences, because the labels depend on the context

$\hat y_t=f_{MLP}(x_t)$ will not work.

## Recurrent Neural Network

abbr. RNN

key idea: introducing an internal/hidden state that is updated as the sequence is processed.

apply recurrence formula (same function and same set of parameters at every time step).

$h_t=f_{RNN}(h_{t-1},x)$

$\hat y_t=f_{MLP}(h_t)$

### Training RNNs

words -> word embedder -> RNN -> MLP -> predicted labels

loss = predicted labels - true labels

- same RNN/MLP being applied at every timestep
- training is hard/slow.

Solution

- use an extension of RNNs called Long Short-Term Memory (LSTM) networks
- LSTM have memory allowing them to preserve information over many timesteps
- Similar to ResNet

### Sentence embeddings with RNN/LSTM

use the last hidden state as encoding of whole sentence/sequence

applications include sentence classification

can also use sentence embedding for text retrieval, sentence comparison etc.

**input could also be image embeddings**

- action recognition in video
- embed each frame using CNN, then use these embeddings instead of word embeddings.

## Language modeling

language modeling is like a sequence labeling task, where each label is the next word!

this is an autoregressive model (output from previous timestep used as input in current timestep).

## Sequence-to-sequence models

translation is not sequence labeling because imput sentence and output sentence could have different lengths

while input sequence must be encoded into a single vector $h_{encoder}$.

decoder = conditional LM:

- it is autoregressive and predicts the next token
- note that it is conditioned on $h_{encoder}$.

**conditional LM can also be used in image captioning**

- we could train a LM to be conditioned on anything really

### Problem with Seq2seq models

You can't cram the meaning of a whole f\*\*king sentence into a single f\*\*king vector - Ray Monney.

## Solution: Attention

for each word in the output sentence is identify the important and relevant words from the input sentence and assign higher weights to these words, enhancing the accuracy of the output prediction.

识别重要的单词（集中注意力）

Basic idea

- encode each word in the input sentence into a vector
- when decoding, perform a linear combination of these vectors, weighted by attention weights
- use this combination in picking the next word

Key/value/query concept:

analogous to retrieval system. when searching for videos on YouTube, the search engine will map query against a set of keys associated with candidate videos in their database, then present the best matched videos (values).

start by matching the query against all keys (using dot product). Makes sense that "ich" gets the highest score, because it's German for "I".

Contextualized word embedding = linear combination （这个线性组合的系数实际上应该是 $\text{Key vectors}\cdot\text{Query vectors}$ 的结果）of $\text{Value vectors}$

With attention we use all words of the input to predict the next token, not just depending on $h_{encoder}$.

### Attention in image Captioning

features 可以表示成 keys and values 矩阵。

keys 矩阵变换成 attention weights

values 变换成 contextual image embedding

注意力机制在图像处理上也能起到一些着重强调重要像素的作用。

### Self-attention

each element in the sentence attends to other elements - context sensitive encodings

RNN/LSTM have an important limitation: each sequence must be treated one element at a time. Both the encoder and the decoder have to wait till the completion of $t-1$ steps to process the t-th step. So, when dealing with huge corpus it is very time consuming and computationally inefficient.

## Transformers

seq2seq model based entirely on attention (fast!!!)

self-attention: each laayer combines words with others.

multi-headed attention: 8 attention heads learned independently.

scaled dot-product attention: remove bias in dot product when using large networks

positional encodings: make sure that even if we don't have RNN, can still distinguish position

### Encoder block

first encoder block receives a seq of word embeddings as input

remaining encoder blocks receive output from previous encoder block as input.

### Self-attention block

$$
Attention(Q,K,V)=softmax(\frac{QK^T}{\sqrt{d_k}})V\\
q_i=W_qx_i\\
k_i=W_kx_i\\
v_i=W_vx_i\\
$$

### Decoder

Keys and values are computed from encoder embeddings

Queries are computed from decoder embeddings

### Masked multi-head attention

in the decoder the self-attention layer is only allowed to attend to earlier positions in the output sequence. This is done by masking future positions before the softmax step in the self-attention calculation.

positional encoding: usually works fine if they are hard-coded

simple hard-coded solution: rotational positional encoding vectors

## RNN vs Transformer

RNN:

:white_check_mark: LSTM work reasonably well for long sequences.

:no_entry: expects an ordered sequence of inputs

:no_entry: subsequent hidden states can only be computed after the previous ones are done.

Transformer:

:white_check_mark: good at long sequences

:white_check_mark: can operate over unordered sets or ordered sequences with positional encodings

:white_check_mark: parallel computation: all alignment and attention scores for all inputs can be done in parallel

:no_entry: require a lot of memory: $N\times M$ alignment and attention scalers need to be calculated and stored for a single self-attention head.

# Some SOTA Model

## GPT-3

transformer-based, large language model with 175M parameters

trained to perform next word prediction.

trained on

- common crawl dataset
- WebText dataset collected by scraping links over a longer period of time
- two Internet-based books corpora
- English-language Wikipedia

Before GPT-3:

- pre-train language model to perform next word prediction or masked word prediction

- then fine-tune on labeled dataset to solve specific downstream task

### In-context learning

unsupervised pre-training

prompting

zero-shot

- the model predicts the answer given only a natural language description of the task. no gradient update are performed.

one-shot

- in addition to the task description, the model sees a single example of the task. no gradient update.

few-shot

- in addition to task description, the model sees a few examples of the task. no gradient update.

### GitHub Copilot

it uses OpenAI Codex to suggest code and entire functions in real-time, right from your editor

## Other models

### Side-note: self-supervised learning

### image captioning using Transformers

### Vision Transformer aka ViT

- image is split into fixed size patches, each of them are then linearly embedded, position embeddings are added, and the resulting sequence of vectors is fed to a standard Transformer encoder. In order to perform classification, a standard approach of adding an extra learnable "classification token" to the sequence is used.

### Contrastive Language-Image Pre-Training

aka CLIP

learns association between an image and a piece of text

this can be achieved by training two separate transformer encoders for text snippet and the image.

the encoded image as well as the text features can then be compared for their respective similarity by constructing a cosine-similarity matrix

### DALL-E and Stable Diffusion

use text encoding from CLIP as the target encoding

encoding 用 CLIP，解码用 Inverting CNN

Stable Diffusion: the image information creator makes two inputs

1. text embedding
2. random noise

why not use GAN? Because it lacks of diversity in image generation, mode collapse, hard to train etc.

Diffusion models - basic principle

- forward diffusion process: the image is corrupted by gradually introducing noise until the image becomes complete random noise.
- reverse diffusion process: in the reverse process, a series of Markov Chains are used to recover the data from the Gaussian noise by gradually removing the predicted noise at each time step.

### Latent Diffusion Models

operate in the pixel space are demanding in terms of compute and memory

an LDM applies the diffusion processes in the latent space instead of pixel space while incorporating semantic feedback from a Transformer

LDMs are not only memory efficient but also produce diverse, highly detailed images which preserve the semantic structure of the data.

### Conditioned Latent Diffusion

the image generation task is conditioned on a prior

denoising U-Net autoencoder with cross-attention

### Multimodal Machine Learning

Transformers work on any kind of sequence data - just requires some kind of embedding

multimodal learning refers to the process of learning representations from different types of modalities using the same model