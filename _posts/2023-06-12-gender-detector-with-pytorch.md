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

### How to work with data with Pytorch

Pytorch library is a very rich framework with libraries that fit your need according to the type of problem you want to solve. For instance,`Torchaudio` for audio recognition, `Torchvision` for images, `Torchtext`for NLP, `Torchgeo` for satellite images, and so on. Since our problem is image classification, we will use the `Torchvision` library. This library has has a very straight forward (but flexible) process about reading data: your first (1) create your dataset, than you transform (2) the data, augment (3) it if necessary, and finally, add it to a dataloader (4) before training. This process can be ilustrated in the image bellow

<p align="center">
  <img src="/img/basic_dataloader_pipeline.png">
  Source https://arcwiki.rs.gsu.edu
</p>

### Building your custom set

`Torchvision` is a package in the PyTorch library containing computer-vision models, datasets, and image transformations. A famous dataset in the domain of Machine Learning (the MNIST) dataset, could be easily load like this : 

```python
import torchvision
import torchvision.datasets as datasets

# Load train set
mnist_trainset = datasets.MNIST(root='./data', train=True, download=True, transform=None)
# Load test set
mnist_testset = datasets.MNIST(root='./data', train=False, download=True, transform=None)
```

Notice that `datasets.MNIST` is already provided out of the box to us. But in real data problems, that is not always the case. We will have to create our own datataset. But, as I said, Pytorch makes it quite easy :-)


