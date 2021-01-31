---
title: 细粒度识别相关调研
date: 2020-09-03 22:54:00
tags: [深度学习,细粒度识别]
toc: true
reward: true
---

> **毕业研究方向**：蝴蝶的细粒度识别，检测 + 识别，创新点设定在如何提高小样本的识别，最后应用做个小程序。

## 一、什么是细粒度

简单来说 fine-grained 就是细粒度，fine-grained classification 是细粒度的图像分类。与coarse-grained classification（粗粒度）相比，粗粒度是分辨是猫还是狗——**细粒度是分辨狗这个类别下，这张图是 1.吉娃娃 2.萨摩耶 ... n.哈巴狗**

与 coarse grained classification 不同，fine-grained classification 往往需要professional expert 去标注数据，这使得数据更加昂贵。

<!-- more -->

现在已有的一些 **fine-grained-classification** 方法中，非常直观的借用 category-level information，即“一只吉娃娃以**细小的体型、大眼睛、特别大的耳朵**和**直竖的尾巴**为特征”，结合视觉信息与文字描述信息。

还有一些使用 **transfer learning** 的方法。由于 ImageNet 里已经包含很多种狗或鸟的图片，与 Stanford Dogs（狗类）或者 CUB（鸟类）有较大重叠部分——这使得单纯的 fine-tuning 都可以得到约等于77%的准确率。那么，我们只需要在此基础上增加一些 domain adaptation 或者 instance-level weighted 的方法就可以将 ImageNet 上训练好的模型“迁移”到适合fine-grained classification的问题上。[[1]](https://openaccess.thecvf.com/content_ICCV_2017/papers/Gebru_Fine-Grained_Recognition_in_ICCV_2017_paper.pdf "[1]")

另一类经典的方法是类似于“**注意力机制**”的思想，关注 **informative part of an image**.比如明显可以区分对于区分很多鸟类来说，头这个部位可能是更加重要的，那我们就重点关注喙。将图片分为 image-level 与 part-level (或者说head-level),分别进行特征表示，然后 fusion 进行分类。

