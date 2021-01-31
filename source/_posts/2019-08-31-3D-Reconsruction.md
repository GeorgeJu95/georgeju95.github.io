---
title: 3D重建的一些学习记录
date: 2019-08-31 16:56:36
categories: 
toc: true
tags: [3D重建]
---

## insight 3D使用

[insight3D软件地址](http://insight3d.sourceforge.net)
<!-- more -->
1. 输入的照片不需要经过测量标定，但要满足以下条件
	* 照片应该**对焦**，不能模糊
	* 相邻的照片间应该有很多的**重叠部分**
	* 照片序列的**角度差异**不宜太大
	* 应该使用具有**丰富细节和纹理**的场景的照片
	* **避免只有一整块平面**的照片，这样子的照片软件无法确定焦距
	* **不要裁剪**照片
	* **3的规则**：重建场景的每一个部分都必须出现在至少3张照片中
2. 加载照片（使用ifl文件一次性加载）到软件中
3. 自动匹配以及相机标定：该软件可以不需要任何额外信息就能获得所以的相机参数（比如相机的**位姿**以及**焦距**），需要两个步骤来完成
   * 首先找到照片中的特征点(importan points)，然后试图在其他照片中去**匹配**这些特征点。
   * 之后**标定**相机，获得相机相关的参数（位姿：位置+朝向，以及内部参数）
4. **匹配(start matching)**完成后照片上会出现很多小方格，代表已经匹配的点，悬浮在其上会显示所有匹配点的缩略图。自动匹配也会出现误匹配，由于标定算法的鲁棒性，这些outliers不影响结果
5. 如果出现只有小部分匹配点，除了检查输入的照片是否满足上面的那些条件外，通过调整matching选项，也有一定作用。比如提高照片分辨率；如果你有9张或以上图片，可以选择 Just neighbours(linear sequence) 进行**线性序列匹配**。
6. 之后进行**标定(automatic calibration)**，完成后可能没有什么变化，依次点击Edit > Mode... > Overview mode，之后可以旋转的3D视角。当前照片的三角锥体（即相机的朝向）被高亮
7. 依次点击Modelling > Triangulate all vertices，会计算所有的匹配点的位置。之后会出现3D点云，通过点云重建场景。有时候点云看起来不成型或者有很多噪声，这可能是由于存在许多误匹配，依次点击Modelling > Triangulate, only trusted 选择只计算那些大概率被正确匹配的点
8. Edit > Mode... 里面有三个模式，overview mode可以看场景的三维旋转，inspection mode可以通过上下左右键在场景中移动，Shot mode就是输入的照片序列。
9. 左边栏的points creator可以手动在所有照片中选择匹配点，之后Modelling > Triangulate user vertices，点变绿说明正确匹配并计算。可以在照片中自己选择点之后，使用polygons creator选项构造多边体（比如选择polygons creator后选择一面墙的四个角落点，会出现紫色边界线，之后Enter确定，边界线变白）。注意，一定要在两张图像中构造匹配点并把多边体构造出来，才能通过Image > Generate textures产生有纹理的模型。
10. 保存project为.i3d文件，导出模型为.vrml文件（可用于其他3D重建软件）

## 商业以及不开源软件

Pix4Dmapper, Autodesk 123D Catch, PhotoModeler, VisualSFM

## Open Source for Research

### COLMAP

### Open MVG

### Bundler

### VisualSFM+CMVS+MeshLab

详见[github](https://github.com/DongfeiJi/3DReconstruction-with-SFM)

