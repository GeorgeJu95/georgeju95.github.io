---
title: 论文阅读：Borrowing Treasures from the Wealthy:Deep Transfer Learning through Selective Joint Fine-Tuning
date: 2019-12-27 18:17:36
categories: 
toc: true
tags: [小样本深度学习,深度学习,paper]
---

[2017_CVPR_Borrowing treasures from the wealthy: deep transfer learning through selective joint fine-tuning](https://arxiv.org/abs/1702.08690)

本篇论文发表于CVPR2017，作者为香港大学的 Weifeng Guo 和 Yizhou Yu，它主要想解决的是传统神经网络训练方法需要大量样本的局限。作者提出了一个叫做 Selective joint fine-tuning 的迁移学习方法，也就是目标任务的标记样本少，但是有一个源域有大量的标记样本。从源域训练集中挑选一些样本，这些样本的低维特征与目标域很相似，然后将源域挑选出的样本与目标域的样本放在一起，进行jointly fine-tuning。结果证明，这种做法是很有效果的。

<!-- more -->

# Introduction

* 如果用少量的数据训练深度网络，那么它的性能可能还不如传统方法。少量的数据并不能够展现出 **sample diversity**，所以比较容易 **over-fitting**

> Training a deep network with insufficient data might even give rise to inferior performance in comparison to traditional classifiers fed with handcrafted features. Fine-grained classification problems, such as Oxford Flowers 102 [29] and Stanford Dogs 120 [19], are such examples. The number of training samples in these datasets is far from being enough for training large-scale deep neural networks, and the networks would become overfit quickly.

* **fine-tuning** 也可以认为是一种简单的 **transfer learning**， 对于 fine-tuning 来说，迭代次数应该少，不然还是容易出现 **over-fitting**

> Solving the overfitting problem for deep convolutional neural networks on learning tasks without sufficient training data is challenging [39]. Transfer learning techniques that apply knowledge learnt from one task to other related tasks have been proven helpful [30]. In the context of deep learning, fine-tuning a deep network pre-trained on the ImageNet or Places dataset is a common strategy to learn taskspecific deep features.This strategy is considered a simple transfer learning technique for deep learning. However, since the ratio between the number of learnable parameters and the number of training samples still remains the same, fine-tuning needs to be terminated after a relatively small number of iterations; otherwise, overfitting still occurs.

* 利用迁移学习的问题：源域有足够的样本和标记，目标域只有少量样本和标记（或者有大量样本，但是标记很少，甚至没有标记），源域的样本和目标域的样本分布并不相同，有一定的相关性。

> The original learning task without sufficient training data is called the target learning task, $\mathcal T_t$. To boost its performance, the target learning task is teamed up with another learning task with rich training data. The latter is called the source learning task, $\mathcal T_s$. Suppose the source learning task has a large-scale training set $\mathcal D_s$, and the target learning task has a small-scale training set $\mathcal D_t$.

* 文章的核心想法就是，**从源域训练集中挑选一些样本**，这些样本的**低维特征与目标域很相似**，然后将源域挑选出的样本与目标域的样本放在一起，进行**jointly fine-tuning**。这样做的逻辑是，如果把源域中的所有样本拿来做 fine-tuning，源域中与目标域中不相似的样本会干扰目标域特征的学习。

> Our core idea is to identify a subset of training images from $\mathcal D_s$ whose low-level characteristics are similar to those from $\mathcal D_t$, and then jointly fine-tune a shared set of convolutional layers for both source and target learning tasks. The source learning task is fine-tuned using the selected training images only. Hence, this process is called selective joint fine-tuning. The rationale behind this is that the unique low-level characteristics of the images from $\mathcal D_t$ might be overwhelmed if all images from $\mathcal D_s$ were taken as training samples for the source learning task.

* 如何选择低维特征：使用线性或非线性滤波器组的响应来定义在低维特征上的相似度。**Gabor滤波器**构成线性滤波器组，**预训练CNN的某些层的完整内核集**构成非线性滤波器组。作者使用滤波器组响应的直方图作为图像描述符来搜索具有相似低维特征的图像。

> Since kernels followed with nonlinear activation in a deep convolutional neural network (CNN) are actually nonlinear spatial filters, to find sufficient data for training high-quality kernels, we use the responses from existing linear or nonlinear filter banks to define similarity in low-level characteristics. Gabor filters [28] form an example of a linear filter bank, and the complete set of kernels from certain layers of a pretrained CNN form an example of a nonlinear filter bank. We use histograms of filter bank responses as image descriptors to search for images with similar low-level characteristics.

* 为什么根据低维特征来选择图片：1、较低的卷积层构成了整个卷积网络的基础。2、具有相似低维特征的图像可具有非常不同的高维语义内容。

> First, low-level characteristics are extracted by kernels in the lower convolutional layers of a deep network. These lower convolutional layers form the foundation of an entire network, and the quality of features extracted by these layers determines the quality of features at higher levels of the deep network. Sufficient training images sharing similar low-level characteristics could strength the kernels in these layers. Second, images with similar low-level characteristics could have very different high-level semantic contents. Therefore, searching for images using low-level characteristics has less restrictions and can return much more training images than using high-level semantic contents.

* 作者提出的 source-target selective joint fine-tuning scheme 的优越性：1、防止很快过拟合。2、共享卷积层的内核训练更加鲁棒性，产生高判别特征。

> First, since convolutional layers are shared between the two learning tasks, the selected training samples for the source learning task prevent the deep network from overfitting quickly. Second, since the selected training samples for the source learning task share similar lowlevel characteristics as those from the target learning task, kernels in their shared convolutional layers can be trained more robustly to generate highly discriminative features for the target learning task.

# 相关工作

* 卷积神经网络相关 
[Hierarchical deep convolutional neural networks for large scale visual recognition.][1]

* 特征提取、Fine-tuning 相关
[How transferable are features in deep neural networks?.][2]

* 迁移学习
   1. [Learning transferrable knowledge for semantic segmentation with deep convolutional neural network.][3]

   2. [Simultaneous deep transfer across domains and tasks.][4]

   3. [Understanding Deep Image Representations by Inverting Them.][5]

# Selective Joint Fine-tuning

![sjft](/assets/blogImg/sjft.png)

  1. 选择 **source domain**：ImageNet ILSVRC, Places, and MS COCO。
  2. 选择低维特征： Gabor filter 和 kernels in the convolutional layers of AlexNet pre-trained on ImageNet，然后计算它的 histograms作为 descriptor。
  3. 选择网络结构：AlexNet， VGG， ResNet，文章用的是 ResNet，可以看下这篇论文 [Deep Residual Learning for Image Recognition][6] 了解一下。
  4. 源域的问题和目标域的问题是各自的 cost function （两个 cost function 是同等的权重？是同时更新的还是迭代更新的？）。

<p style="color:#ff0000;">接下来是重点</p>

* (Filter Bank) Gabor filter： 4 scales， 6个direction， 实部虚部分开，总共48个Gabor filter。

> For each of the real and imaginary parts, we use 24 convolutional kernels with 4 scales and 6 orientations. Thus there are 48 Gabor filters in total.

* (Filter Bank) kernels in convolutional layers： 利用 AlexNet pre-trained on  ImageNet的第一层和第二层的所有 kernel。

> In practice, we use all kernels (and their following nonlinear activation) from the first and second convolutional layers of AlexNet pre-trained on ImageNet as our second choice of a filter bank.

* Image Descriptor： 对每个 response map，计算目标域中的最大值和最小值，然后在最大和最小值直接划分许多 bin，bin 的 width 是不固定的，目的是为了让每个 bin 基本上都能落入一样多的 pixel (防止了大量的pixel 落入同一个 bin)。将一幅图所有的 response map 的 histogram 叠在一起构成特征向量，作为 descriptor。

> Let $\mathbb C_i(m, n)$ denote the response map to the $i$-th convolutional kernel or Gabor filter in our filter bank, and $\phi_i$ its histogram. To obtain more discriminative histogram features, we first obtain the upper bound $h^u_i$ and lower bound $h^l_i$ of the $i$-th response map by scanning the entire target domain $\mathcal D_t$. Then the interval $h^l_i$, $h^u_i$ is divided into a set of small bins. We adaptively set the width of every histogram bin so that each of them contains a roughly equal percentage of pixels. In this manner, we can avoid a large percentage of pixels falling into the same bin. We concatenate the histograms of all filter response maps to form a feature vector, $\phi^k = \left\\{\phi_1,\phi_2,,\phi_D\right\\}$, for image $x_k$.

* Nearest Neigh bor Ranking： convolutional layer 每一层的的 kernel 都不同，因此对 kernel 需要用每一层总的 kernel 数目进行归一化。

> To ensure equal weighting among different convolutional layers during nearest neighbor search, each histogram of kernel responses is normalized by the total number of kernels in the corresponding layer.

* Hard samples in the target domain：对 hard samples 应该选择更多的 nearest neighbor。文章中使用了迭代的方法，计算 target samples 在第 m 次迭代后，被分为某一类的概率值的确定性，这个确定性用信息熵描述。对于难分的样本，增加 nearest neighbor 的方法如下：

	* 如果预测标记与真实标记不同， 那么增加其 nearest neighbor，增加$4\mathcal K_0$个 nearest neighbor。

	* 如果预测标记与真实标记相同，但是属于难分样本，增加 $2\mathcal K_0$ 个 nearest neighbor。

	* 如果预测标记与真实标记相同，不属于难分样本，不增加 nearest neighbor。

	* **$\mathcal K_0$ 是初始给每个 target sample 选择 nearest neighbor 的数目。**


# 实验相关

* 数据集

  Large-scale image datasets, such as the [ImageNet ILSVRC dataset](https://arxiv.org/abs/1409.0575), [Places](http://places.csail.mit.edu/places_NIPS14.pdf), and [MS COCO](https://arxiv.org/abs/1405.0312)

* 参数设置

  > Every mini-batch can include 20 224×224 images using a modified implementation of the residual network. We include randomly chosen samples from the target domain in a mini-batch. Then for each of the chosen target sample, we further include one of its retrieved nearest neighbors from the source domain in the same mini-batch. We set the iter size to 10 for each iteration in Caffe [17]. The momentum parameter is set to 0.9 and the weight decay is 0.0001 in SGD. During selective joint fine-tuning, the learning rate starts from 0.01 and is divided by 10 after every 2400—5000 iterations in all the experiments. Most of the experiments can finish in 16000 iterations.
  
* source domain 至少需要200,000张 retrieved images，照片太少容易过拟合。而且$\mathcal K_0$设置的太大也会造成学习表现急剧下降。
* 作者在 **Fine-grained Object Recognition**、**General Object Recognition** 以及 **Scene Classification** 任务上表现都很不错，而且当每个类别的目标训练样本较少时，训练效果会提升。这是因为目标样本的多样性需要源域更多样本的支持。
* 作者还做了 **Ablation Study**：1、不用 fine-tuning，取而代之一个只用目标域，一个只用 select joint training，然后从头开始训练。结果是后者更佳，但仍然是不如 fine-tuning pre-trained networks，这是因为要从头训练神经网路，样本还是太少。2、不从源域中挑选训练样本，而是使用所有的样本。结果是使用所有样本效果变差，这说明使用更少但是更相关的源域样本效果会更好。3、不从源域中挑选训练样本，而是随机使用样本，效果变差。4、对于目标域的 hard training samples 不使用上面的迭代方法，而是对所有样本都使用同样的$\mathcal K_0$，效果变差。5、用 Gabor filter bank 代替 AlexNet pre-trained on  ImageNet的第一层和第二层的所有 kernel，效果变差。说明从不同数据集学习到的滤波器比纯统计滤波器更有威力。6、去掉 源域，不用想效果肯定变差。


[1]: https://arxiv.org/abs/1410.0736
[2]: https://arxiv.org/abs/1411.1792
[3]: https://arxiv.org/abs/1512.07928
[4]: https://people.eecs.berkeley.edu/~jhoffman/papers/Tzeng_ICCV2015.pdf
[5]: https://arxiv.org/abs/1412.0035
[6]: https://arxiv.org/abs/1512.03385