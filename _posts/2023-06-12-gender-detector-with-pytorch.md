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

Pytorch library is a very rich framework with libraries that fit your need according to the type of problem you want to solve. For instance,`Torchaudio` for audio recognition, `Torchvision` for images, `Torchtext`for NLP, `Torchgeo` for satellite images, and so on. Since our problem is image classification, we will use the `Torchvision` library. This library has has a very straight forward (but flexible) process about reading data: your first (1) create your dataset, than you transform (2) the data, augment (3) it if necessary, and finally, add it to a dataloader (4) before training. This process can be illustrated by the image bellow: 

<p align="center">
  <img src="/img/basic_dataloader_pipeline.png">
  Source https://arcwiki.rs.gsu.edu
</p>

Before we talk about how to load data with Pytorch, I will briefly present the application and the neural network architecture used in this work 

### Application architecture

As stated before, the idea is to have a web application that takes live screenshots from the user's webcam performs live predictions as a result. The application was built according to the following schema :

<p align="center">
  <img src="/img/app1_architecture.png" width="500">
  <p align="center"><strong>Application main architecture</strong></p>
</p>

In this post, we will focus only on the Pytorch side, which is the aim of this post. However, it is import to keep in mind that models applied to real cases might suffer from some sort of technical limitation. These limitations include, but not limited to, poor infrastructure resources, nature of training data, model complexity or even, deployment issues. We will come to that later

### Neural network architecture

The architecture of a neural network is core of deep learning application, since it can provide the most optimized way to deal if the data thought the many layers of input neurons. Our architecture is the same used at 
{% cite Levi2015AgeAG %}

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

Notice that `datasets.MNIST` is already provided out of the box to us. But in real data problems, that is usually not the case. Thus we will have to create our own datataset. But, as I said, Pytorch makes it quite easy :-)

In order to create your own data set, you must create a class that inhertis the `torch.utils.data.Dataset` abstract class. Our custom dataset class must also override the methods `__len__`  and `__getitem__`. They are respectively responsible for returning the size of the dataset and provide support for the indexing samples. 

Let's create our custom dataset called GenderDataSet as follows :

```python
class GenderDataset(Dataset):

    def __init__(self, image_paths, classes,transform=None):
        self.image_paths = image_paths
        self.transform = transform
        self.classes = classes

    def __len__(self):
        return len(self.image_paths)

    def __getitem__(self, idx):
        # Select images by path and indexes
        image_filepath = self.image_paths[idx]
        image = read_image(image_filepath)
        # Create dictionary for class indexes
        idx_to_class =  {i:j for i, j in enumerate(self.classes)}
        class_to_idx =  {value:key for key,value in idx_to_class.items()}
        # Replace split char by '/' on unix systems
        label = image_filepath.split('\\')[-2]
        label = class_to_idx[label]
        # Appy transform if there any is provided
        if self.transform:
            image = self.transform(image)
        # Return image and its label
        return image, label
```
When we instantiate our dataset, we added 3 more class attributes : `image_paths`, `classes` and `transform`. Remember about why I chose this dataset ? Since the dataset contains folders per class, and each class has a `train` and `test` folder, all we have to do is to point where the train and test folders are. That implementation will take care of of rest, for each class present in the root folder :-)

So we instantiate the model {% cite ruby %}.

   #  Instantiating model
    conv_net = ConvNet()
    model = ConvolutionalNeuralNet(conv_net)
    print(conv_net)
    
    
{% bibliography %}
