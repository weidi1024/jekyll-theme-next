---
title: Faster R-CNN阅读
date: 2019-07-23 22:00:00
categories: Object_Detection
tags: Object_Detection Faster_R-CNN
mathjax: false
---

**重拾Faster R-CNN**   
之前阅读过这篇文章，但是阅读的比较泛，也没有很好地去记录，最近需要用到Faster R-CNN，就想着再次阅读一次，主要是简单的翻译和总结，一来可以加深对文章某些细节的理解，二来可以做一些记录，方便以后查阅。


**文章地址：**    
地址1：arxiv：[Faster R-CNN: Towards Real-Time Object Detection with Region Proposal Networks](https://arxiv.org/abs/1506.01497)   
地址2：nips：[Faster R-CNN: Towards Real-Time Object Detection with Region Proposal Networks](http://papers.nips.cc/paper/5638-faster-r-cnn-towards-real-time-object-detection-with-region-proposal-networks.pdf)
代码：[https://github.com/rbgirshick/py-faster-rcnn](https://github.com/rbgirshick/py-faster-rcnn)

# 摘要
先进的目标检测算法依赖于区域提取算法来假设目标的位置。SPPNet和Fast R-CNN等先进的算法降低了检测网络的运行时间，但是区域提取部分的计算也是一个瓶颈。本文中，我们提出了一个与检测网络共享卷积特征的区域提取网络(Region Proposal Network，RPN)，使得几乎无代价的区域提取成为可能。RPN是一个全卷积网络，同时预测每个位置的目标位置和置信度。RPN可以端到端的训练，可以产生高质量的候选区域，用来给Fast R-CNN进行检测。通过共享RPN和Fast R-CNN的基础网络，我们将这两个网络合并成一个统一的网络，使用最近流行的“注意力”机制，RPN告诉网络在何处寻找目标。对于使用VGG-16作为基础网络，我们的检测系统在GPU上的帧率为5fps，同时在PASCAL VOC 2007, 2012, and MS COCO数据集上获得了最先进的目标检测精度，我们的RPN网络仅仅提供300个候选区域。在ILSVRC和COCO 2015的比赛中，Faster R-CNN和RPN是在几个轨道上获得第一名的基础。代码已经公开。

# 引言
**这部分简读**    
      基于区域建议的方法的瓶颈：区域建议的方法和基于区域提取的CNN推动了目标检测领域的发展。现在区域提取部分是测试阶段的速度瓶颈。   
一种解决方法：把算法从CPU迁移导致GPU上，但是这样做会忽略了对网络底层的特征的利用，错过了共享参数的机会。   
本文的方法：本文中，我们证明了基于深度卷积神经网络的区域提取方法是一个很好的解决方案，相对于检测网络的运行时间，其运行时间几乎是可以忽略的。区域提取的成本很小，10ms每幅图。   
共享卷积特征的可能性：可以在卷积特征图上加入了模块进行区域提取，不影响原来的结构。可以根据区域建议任务进行端到端的训练。   
RPN的优点：RPN的设计是为了广泛提取具有不同尺寸和纵横比的候选区域，与常用的方法[8], [9], [1], [2]不同，它们使用图像金字塔或者滤波器金字塔等策略，我们引入了新的“anchor”框，可以作为多种尺度和纵横比的参考。我们的方案可以被认为是a pyramid of regression references，它避免了枚举多个尺度或纵横比的图像或过滤器。当使用单尺度图像进行训练和测试时，该模型性能良好，提高了运行速度。    
训练策略：为了将RPN与Fast R-CNN统一起来，我们设计了一种训练方法，交替对RPN和Fast R-CNN进行微调，这种方法收敛速度快，可以让RPN与Fast R-CNN共享基础网络。     
实验结果优秀：PASCAL VOC的结果精度和速度都最好。第一版文章发布后，基于RPN和Faster R-CNN，出现了很多优秀的算法，如3D object detection [13], part-based detection [14], instance segmentation [15], and image captioning [16]。其他比赛中也取得了优异的性能。这些结果表明，我们的方法不仅速度快，而且精度高。

# 相关工作
**这部分简读**    
**候选目标区域Object Proposals**。关于候选目标区域有大量的文献资料，可以参考这几篇综述[19], [20], [21].广泛使用的候选区域提取方法基于grouping super-pixels (e.g., Selective Search [4], CPMC [22], MCG [23]) and sliding windows(e.g., objectness in windows [24], EdgeBoxes [6]).的方法。候选目标区域的方法作为独立于检测器的外部模块，比如选择性搜索（Selective Search）检测器，R-CNN和Fast R-CNN。
**基于深度网络的目标检测**。R-CNN的方法端到端地训练CNN，将候选区域分为目标或者背景类别。R-CNN主要作为一个分类器，不进行候选区域的预测，除了对边框进行精细回归。有文章[20]说明，它的性能主要取决于候选区域模块的性能。目前已经有一些文章[25], [9], [26], [27]使用深度网络来预测候选区域。在OverFeat[9]中，使用全连接层来为定位任务预测边框，假定图像中只有一个目标。全连接层后来转变成卷积层用来检测多类目标。在MultiBox方法[26],[27]中，通过网络最后一层全连接层同时预测多个类无关的候选边框，推广了OverFeat中的单一边框的方法。



# Faster R-CNN

# 实验

# 结论