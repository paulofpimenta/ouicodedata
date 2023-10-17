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

### Get the dataset first. But which one and why ?

It is not difficult to find some available datasets containing human faces. The sources are numerous : The [UTKFace](https://susanqq.github.io/UTKFace/); the famous [CelebA](https://mmlab.ie.cuhk.edu.hk/projects/CelebA.html) database with more than 200 thousand faces of celebrities; [DigiFace](https://github.com/microsoft/DigiFace1M) with its 1.2 million free from privacy violations images, and so on. A good list can be found [here](https://datagen.tech/blog/face-datasets/). In this post, I decided to work with a dataset this dataset [here](https://www.kaggle.com/datasets/ashwingupta3012/male-and-female-faces-dataset/data) for three reasons. The first is that this dataset, as many others, has  with a public domain license, but is also very used in kaggle notebooks. The second reason, is how the data is presented and organized.  The other datasets have either spreadsheets with annotations containing name files and the classes thar each file belong to, or, in some cases, classes are not divided by folders. Since my object is to load data from custom dataset (next section :-)), I wanted a dataset that was more structured into folders and classes. And the final reason is the image quality : this dataset does not contain high quality images. Which is is quite well, considering that our input images will come from web cam, that usually have low resolution images. Thus, in this case, a model trained with nice and artistic images may not reflect the context of the application. 

### How to read data with Pytorch

Pytorch is very straigh foward

<p align="center">
  <img src="/img/basic_dataloader_pipeline.png">
</p>

[ACIDS Cluster](https://arcwiki.rs.gsu.edu/en/dali/pytorch_basic_data_loader)

