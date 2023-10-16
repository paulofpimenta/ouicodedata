---
layout: post
title:  "Gender detection with Pytorch"
date:   2023-06-12 20:15:09 +0800
comments: true
tags:
- Deep Learning
- Pytorch
---
Last month I spent quite some time diving into the wonderful world of Pytorch library for deep learning. My objective is to post a series of applications with pytorch features. But for now I will focus on how to build an gender detection application with Pytorch using the webcam as input

### Get the data first

It is not difficult to find some available datasets containing human faces. The [UTKFace](https://susanqq.github.io/UTKFace/); the famous [CelebA database](https://mmlab.ie.cuhk.edu.hk/projects/CelebA.html) with more than 200 thousand faces of celebrities; [DigiFace](https://github.com/microsoft/DigiFace1M) with its 1.2 million free from privacy violations images, and others. A good list can be found [here](https://datagen.tech/blog/face-datasets/). In this post, I decided to work with a dataset with a public domain license that I found on kaggle [here](https://www.kaggle.com/datasets/ashwingupta3012/male-and-female-faces-dataset/data). I chosed this data set because some of the other dataset have files with annotations about objects ages and gender, and the classes are not divided by folders. In the context of this example, I believe it is easier to categorize gender by folder rather than by file annotations, specially if you want to add new face samples.

### How to read data with Pytorch

