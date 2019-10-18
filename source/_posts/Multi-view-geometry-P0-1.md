---
title: 计算机视觉中的多视角几何：第0篇 基础知识：射影几何、变换和估计(第2章)
date: 2019-07-19 10:30:00
categories: 
toc: true
tags: [多视角几何第二版,翻译,计算机视觉]
---

# 本篇大纲

此篇的四章为本书后续章节将要用到的表示、术语和记号打基础，射影几何的概念和表示法是多视图几何分析的核心。例如，使用了齐次坐标就能用线性矩阵方程来表示非线性映射（如透视投影）；而且可以很自然地表示无穷远点，避免了取极限的麻烦。

<!-- more -->

第2章引入二维空间（以下简称2D）的射影变换。这些变换发生在用透视摄像机对平面摄像的时候。该章偏重于入门介绍并为三维空间（以下简称3D）几何铺路。大多数的概念在2D中比3D中更容易理解和可视化。本章介绍**射影变换**，包括它的特殊情况：<u>仿射和相似变换</u>；并把注意力主要集中在从透视图像中恢复**仿射性质**（例如平行线）和**度量性质**（例如线之间的角度）。

第3章覆盖3D射影几何。该几何的推导方式与2D非常相似，当然，由于维数的增加出现了新的性质。这里主要的新几何是**无穷远平面**和**绝对二次曲线**。

第4章介绍**由图像测量进行几何估计**，这是本书的主要论题之一。我们以用点对应来估计射影变换为例来说明估计算法的要素和目的，这些算法将在整本书中被采用。其中重要的问题是：<u>一个代价函数应该最小化什么</u>，比如是代数的或是几何的或是统计的测量，该问题将长篇地加以介绍。本章还打算介绍鲁棒估计的思想，以及这样的技术在变换的自动估计中的应用。

第5章介绍如何评价估计算法的结果。特别是如何计算估计的协方差。

# 第2章  2D射影几何和变换

本章主要介绍理解此书内容所必须的几何概念和记号。某些概念为大家所熟悉，例如消影点的形成和二次曲线的表示，而其余的却比较深奥，例如用虚圆点去消除图像中的射影失真。这些概念在平面（2D）中比较容易理解，因为在2D中它们比较容易可视化。以后本书将涉及的3D几何的内容就是平面情形的简单推广。

具体地说，本章涵盖平面的射影变换几何。这些变换模拟透视摄像机对平面摄像时所产生的几何失真。在透视影像下，某些几何性质被保留，例如保线性（直线被影像为直线），而有的则不被保留，例如平行线一般不被影像成平行线。射影几何为这类影像建模而且提供适于计算的数学表达。

我们先介绍在齐次标记下点、线和二次曲线的表示，以及在射影变换下这些几何实体如何映射。接着介绍无穷远线和虚圆点，并证明它们控制了平面的仿射和度量性质。然后，给出平面矫正的算法，这些算法实现了由图像来计算仿射和度量性质。最后，我们介绍射影变换下的不动点。

## 2.1 平面几何

任何学习过初等数学的人都熟悉平面几何的基本概念。事实上，甚至因为这些概念已成为我们日常生活的部分经验，因此我们总以为它们理所当然地成立。在初等水平上，几何研究点和线以及它们的关系。

按几何纯传统论者的观点，几何研究应该坚持“几何的”或者说与坐标无关的观点。在这种方法中，定理的叙述和证明仅使用几何的公理而不使用代数。经典的欧式方法就是其中的一个例子。但是，自笛卡尔之后，人们认识到几何可以代数化，而且，几何理论的确可以从代数的观点来推导。在本书中，我们将使用混合的方法，有时用几何的而有时用代数的方法。在代数方法中，几何实体用坐标和代数实体描述。例如，一个点等同于某坐标基下的一个矢量；一条直线也等同于一个矢量；而一段圆锥截线（或简称二次曲线）用一个对称矩阵表示。事实上，至少因为语言上的方便，我们经常采用这样的等价表示，即**矢量就是点，对称矩阵就是二次曲线**。在几何中采用代数方法的显著优点是：这种方法导出的结果更容易产生算法以及实际的计算方法。本书主要关注的是计算和算法，它将证明使用代数方法是合理的。

## 2.2 2D射影平面

众所周知，平面上的一点可以用$\mathbb R^2$中的一对坐标$(x, y)$来表示。因此，通常$\mathbb R^2$等同于一张平面。把$\mathbb R^2$看作一个矢量空间时，坐标对$(x, y)$是矢量，也就是说点等同于矢量。本节将引入平面上点和线的**齐次**表示。

#### 行和列矢量

此后，我们将考虑矢量空间之间的线性映射并把这样的映射表示成矩阵。在通常的方式下，一个矩阵和一个矢量的积是另一个矢量，它就是该映射下的**像**。由此引出“列”和“行”矢量的区别。因为矩阵可以被列矢量右乘或被行矢量左乘，在不加说明时，几何实体用列矢量表示。粗体符合如$\mathbf x $总表示列矢量，它的转置是行矢量$\mathbf x^\intercal$。按此约定，平面上的点将表示为列矢量$(x, y)^\intercal$，而不是它的转置：行矢量$(x, y)$。我们记$\mathbf x=(x, y)^\intercal$，该方程的两边都是列矢量。

### 2.2.1 点与直线

#### 直线的齐次表示

平面上的一条直线可用形如$ax+by+c=0$的方程表示，$a$，$b$，$c$的不同值给出不同的直线。因此一条直线也可以用矢量$(a, b, c)^\intercal$表示。直线和矢量$(a, b, c)^\intercal$不是一一对应的，因为，对任何非零常数$k$，直线$ax+by+c=0$与$(ka)x+(kb)y+(kc)=0$相同。因此，对任何非零$k$，矢量$(a, b, c)^\intercal$与$k(a, b, c)^\intercal$表示同一直线。事实上，我们视这两个只相差一个全局缩放因子的矢量是等价的。这种等价关系下的矢量等价类被称为**齐次矢量**。任何具体矢量$(a, b, c)^\intercal$是所属的等价类的一个代表。在$\mathbb R^3-(0, 0, 0)^\intercal$中的矢量等价类的集合组成**射影空间**$\mathbb P^2$。记号：$-(0, 0, 0)^\intercal$表示矢量$(0, 0, 0)^\intercal$不与任何直线对应，因而被排除在外。

#### 点的齐次表示

点$\mathbf x=(x, y)^\intercal$在直线$\mathbf l=(a, b, c)^\intercal$上的充要条件是$ax+by+c=0$。并可用矢量内积形式把它表示为$(x, y, 1)(a, b, c)^\intercal=(x, y, 1)\mathbf l=0$；即把“1”作为增加的最后一个坐标使$\mathbb R^2$中的点$(x, y)^\intercal$表示成3维矢量。注意对任何非零$k$和直线$\mathbf l$，方程$(kx, ky, k)\mathbf l=0$的充要条件是$(x, y, 1)\mathbf l=0$。因而，我们自然把由$k$不为零的不同值所构成的矢量集$(kx, ky, k)^\intercal$看作是$\mathbb R^2$中$(x, y)^\intercal$的一种表示。因此，与直线一样，点也可用齐次矢量表示。一个点的任何齐次矢量的表示形式为$\mathbf x=(x1, x2, x3)^\intercal$，并表示$\mathbb R^2$中的点$\mathbf x=(x1/x3, x2/x3)^\intercal$。于是，点作为齐次矢量同样也是$\mathbb P^2$的元素。

我们得到一个用以确定点在不同直线上的简单方程，即

##### 结论2.1 点$\mathbf x$在直线$\mathbf l$上的充要条件是$\mathbf x^\intercal \mathbf l = 0$.

注意：表达式$\mathbf x^\intercal \mathbf l$就是两矢量$\mathbf x$和$\mathbf l$的内积或标量积，即$\mathbf x^\intercal \mathbf l = \mathbf l^\intercal \mathbf x = \mathbf x \cdot \mathbf l$。一般，我们更喜欢采用转置记号$\mathbf l^\intercal \mathbf x$，偶尔，也用一个“$\cdot$”来表示内积。注意区分一个点的**齐次坐标**：$\mathbf x=(x1, x2, x3)^\intercal$，它是3维矢量；与**非齐次坐标**：$(x, y)^\intercal$，它是二维矢量。

#### 自由度(dof)

显然，为了指定一个点必须提供两个值，即它的$x-$坐标和$y-$坐标。同样，一条直线由两个参数指定（两个独立的比率$\left| a:b:c \right|$）因而有两个自由度。例如，在非齐次表示中，这两个参数可以取为直线的梯度和$y$轴上的截距。

#### 直线的交点

给定两直线$\mathbf l=(a, b, c)^\intercal$和$\mathbf l'=(a', b', c')^\intercal$，我们希望求它们的交点。定义矢量$\mathbf x =\mathbf l \times \mathbf l'$，这里$\times$表示矢量积或叉积。由三重纯量积等式：$\mathbf l \cdot (\mathbf l \times \mathbf l') = \mathbf l' \cdot (\mathbf l \times \mathbf l') = 0$，推出$\mathbf l^\intercal \mathbf x = \mathbf l'^\intercal \mathbf x = 0$。因此，如果把$\mathbf x$视为一个点，则$\mathbf x$同时在两条直线$\mathbf l$和$\mathbf l'$上，因而是两线的交点。这表明：

##### 结论2.2 两直线$\mathbf l$和$\mathbf l'$的交点是点$\mathbf x=\mathbf l \times \mathbf l'$

注意：两直线交点的表示之所以这样简洁是因为采用了直线和点的齐次坐标表示。

例2.3 考虑一个简单问题：求直线$x=1$和$y=1$的交点。直线$x=1$等价于$-1x+1=0$，故有齐次表示$l=(-1, 0, 1)^\intercal$。直线$y=1$等价于$-1y+1=0$，齐次表示是$l'=(0, -1, 1)^\intercal$。由结论1.2得到交点为$$\mathbf x=\mathbf l \times \mathbf l'= \begin{vmatrix} i&j&k \\\\ -1&0&1 \\\\ 0&-1&1 \end{vmatrix} = \begin{pmatrix} 1 \\\\ 1 \\\\ 1 \end{pmatrix}$$它是非齐次点$(1, 1)^\intercal$，正是我们所要求的。

#### 点的连线

过两点$\mathbf x$和$\mathbf x'$的直线的表示式可完全类似地导出。定义直线$\mathbf l = \mathbf x \times \mathbf x'$，并不难验证点$\mathbf x$和$\mathbf x'$都在$\mathbf l$上。因此

##### 结论2.4 过两点$\mathbf x$和$\mathbf x'$的直线是$\mathbf l = \mathbf x \times \mathbf x'$.

### 2.2.2 理想点与无穷远线

#### 平行线的交点

考察两直线$ax+by+c=0$和$ax+by+c'=0$。它们分别用矢量$\mathbf l=(a, b, c)^\intercal$和$\mathbf l'=(a, b, c')^\intercal$表示，其中它们的前两个坐标是一样的。用结论1.2不难算出这两条直线的交点为$\mathbf l \times \mathbf l' = (c'-c)(b, -a, 0)^\intercal$，忽略标量因子$(c'-c)$，得到点$(b, -a, 0)^\intercal$。

现在，如果我们试图求这一点的非齐次表示，就会得到$(b/0, a/0)^\intercal$，它只能解释为该交点有无穷大坐标。一般地，具有齐次坐标$(x, y, 0)^\intercal$的点不与$\mathbb R^2$中任何有限点对应，这一观察与通常平行线交于无穷远的概念相吻合。

例2.5 考察两直线$x=1$和$x=2$。这两线平行，因而交于“无穷远点”。利用齐次表示将它们表示为$\mathbf l=(-1, 0, 1)^\intercal$和$\mathbf l'=(-1, 0, 2)^\intercal$，再由结论1.2求得其交点为$$\mathbf x=\mathbf l \times \mathbf l'= \begin{vmatrix} i & j & k \\\\ -1&0&1 \\\\ -1&0&2 \end{vmatrix} = \begin{pmatrix} 0 \\\\ 1 \\\\ 0 \end{pmatrix}$$这是在$y-$轴方向上的无穷远点。

#### 理想点与无穷远线

当$x3 \neq 0$时，齐次矢量$\mathbf x=(x1, x2, x3)^\intercal$对应于$\mathbb R^2$中的有限点。我们可以把最后坐标为$x3=0$的点加入$\mathbb R^2$。所扩展的空间是所有齐次3维矢量的集合，称为射影空间$\mathbb P^2$。最后坐标为$x3=0$的点被称为**理想点**，或无穷远点。所有理想点的集合可以写成$(x1, x2, 0)^\intercal$，并由比率$x1:x2$指定一个具体的点。注意该集合在一条直线上，称为**无穷远线**，用矢量$\mathbf l_\infty = (0, 0, 1)^\intercal$表示。我们可以验证$(0, 0, 1)(x1, x2, 0)^\intercal=0$.

由结论2.2，我们推出直线$\mathbf l=(a, b, c)^\intercal$与$\mathbf l_\infty$交于理想点$(b, -a, 0)^\intercal$（因为$(b, -a, 0)\mathbf l=0$）。任何一条与$\mathbf l$平行的直线$\mathbf l'=(a, b, c')^\intercal$也交$\mathbf l_\infty$于同样的理想点$(b, -a, 0)^\intercal$，与$c'$的取值无关。在非齐次表示下，$(b, -a)^\intercal$是与该直线相切的矢量，与该直线的法线$(a, b)$相正交，因而它代表该直线的**方向**。当直线的方向改变时，理想点$(b, -a, 0)^\intercal$沿$\mathbf l_\infty$而变化。基于这些理由，无穷远线可以看作是平面上所有直线方向的集合。

注意引入无穷远点概念后点与直线相交的概念得到了简化。在射影平面$\mathbb P^2$中，我们可以不加思索地说任意两条相异直线都相交于一点而任意两个相异的点都在一条直线上，但在标准欧式几何$\mathbb R^2$中却不成立，其中平行线就构成一个特例。

研究$\mathbb P^2$的几何称为射影几何。在无坐标的纯几何研究中，射影几何的无穷远点（理想点）和普通点没有任何区别。但在本书中，为了达到我们的目的，有时将区别理想点和非理想点。因此，无穷远线有时被看成射影空间中的一条特殊直线。

#### 射影平面的模型

一种有益的方法是把$\mathbb P^2$看作$\mathbb R^3$中一种射线的集合。该集合的所有矢量$k(x1, x2, x3)^\intercal$当k变化时形成过原点的射线。这样的一条射线可以看作是$\mathbb P^2$中的一个点。在此模型中，$\mathbb P^2$中的直线是过原点的平面。可以验证两相异的射线共处于一张平面上，而任何两张相异平面相交于一条射线。这类似于两个相异的点唯一确定一条直线，而两条相异的直线总相交于一点。

点和线可以用平面$x3=1$与这些射线和平面集相交得到。如图2.1所示，表示理想点的射线和表示$\mathbf l_\infty$的平面都与平面$x3=1$平行.

![image-2.1](/assets/blogImg/2.1.png)

<font size=2>**图2.1 射影平面的模型.** $\mathbb P^2$的点和线分别表示为$\mathbb R^3$中过原点的射线和平面.$x1x2-$平面上的射线表示理想点，而$x1x2-$平面表示$\mathbf l_\infty$

</font>

#### 对偶

在关于线和点的性质的陈述中，读者也许已经注意到点和线的作用可以怎样互换。特别是，直线和点的基本接合方程式$\mathbf l^\intercal \mathbf x = 0$是对称的，因为$\mathbf l^\intercal \mathbf x = 0$意味着$\mathbf x^\intercal \mathbf l = 0$，其中线和点的位置互相交换了。类似地，两线相交和一线过两点的结论2.2和2.4，当把点和线的作用互换后，其本质是一样的。由此得到 一个如下的一般原理：**对偶原理**：

##### 结论2.6 对偶原理  2维射影几何中的任何定理都有一个对应的对偶定理，它可以通过互换原定理中点和线的作用而导出.

在应用此原理时，接合的概念也必须做适当地转换. 例如，过两点的直线与过两直线的点(即两线的交点)对偶.

注意 : 一且原定理已被证明，就没有必要再去证明它的对偶. 对偶定理的证明不过是原定理证明的对偶.

### 2.2.3 二次曲线与对偶二次曲线

二次曲线由平面上的二阶方程描述. 在欧氏几何中，二次曲线有三种主要类型: 双曲线，椭圆和抛物线(后面将定义的所谓的退化二次曲线除外)，在经典理论中，这三类曲线是不同方向的平面与圆锥相交所产生的截线(退化的二次曲线由过锥顶的平面产生). 但是我们将了解到，在2D射影几何中所有非退化的二次曲线在射影变换下都等价.

在非齐次坐标中，二次曲线的方程是$$ax^2+bxy+cy^2+dx+ey+f=0$$对应于一个二阶多项式. 通过替代$x \mapsto x_1/x_3, y \mapsto x_2/x_3$，"齐次化"得到$$\begin{equation} ax_1^2+bx_1x_2+cx_2^2+dx_1x_3+ex_2x_3+fx_3^2=0 \tag{2.1} \end{equation}$$

或表示成矩阵形式$$\mathbf x^\intercal \mathbf C \mathbf x = 0 \tag{2.2}$$

其中二次曲线系数矩阵$\mathbf C$为$$\mathbf C = \begin{bmatrix} a & b/2 & d/2 \\\\ b/2 & c & e/2 \\\\ d/2 & e/2 & f \end{bmatrix} \tag{2.3}$$

注意二次曲线的系数矩阵是对称的. 它与点和线的齐次表达式一样，重要的仅仅是矩阵元素的比率，因为用一个非零标盘乘$\mathbf C$不会影响上面的方程. 因此，$\mathbf C$是一条二次曲线的齐次表示. 二次曲线有五个自由度，可以视为比率$\left| a : b : c : d : e : f \right|$或等价地视为对称矩阵六个元素减去一个比例因子.

#### 五点定义一条二次曲线

假定我们希望计算过点集$\mathbf x_i$的二次曲线，在二次曲线被唯一确定之前，我们可以指定多少个点? 我们打算用一个确定二次曲线的算法来构造性地回答这个问题. 根据(2.1)可知，每一点$\mathbf x_i$给二次系数提供一个约束，因为如果二次曲线过$(x_i, y_i)^\intercal$，便有$$ax_i^2+b_ix_iy_i+cy_i^2+dx_i+ey_i+f=0$$

该约束可以重写为$$(\ x_i^2\quad x_iy_i\quad y_i^2\quad x_i\quad y_i\quad 1 \ ) \  \mathbf c = 0$$其中$\mathbf c = ( a , b , c , d , e , f)^\intercal$是把二次曲线 $\mathbf C$表示成一个6维矢量.

把五点提供的约束堆积起来，得到$$\begin{bmatrix} x_1^2 & x_1y_1 & y_1^2 & x_1 & y_1 & 1 \\\\ x_2^2 & x_2y_2 & y_2^2 & x_2 & y_2 & 1 \\\\ x_3^2 & x_3y_3 & y_3^2 & x_3 & y_3 & 1 \\\\ x_4^2 & x_4y_4 & y_4^2 & x_4 & y_4 & 1 \\\\ x_5^2 & x_5y_5 & y_5^2 & x_5 & y_5 & 1 \end{bmatrix} \mathbf c = 0 \tag{2.4}$$

因而二次曲线是这个$5\times6$矩阵的零矢量. 它表明一条二次曲线由一般位置的五个点唯一确定(相差一个尺度因子)。这种通过求零空间来拟合一个几何实体(或关系)的方法今后将经常在本书介绍计算的章节中采用.

#### 二次曲线的切线

在齐次坐标下，过二次曲线上点$\mathbf x$的切线$\mathbf l$有特别简单的形式：

##### 结论2.7  过（非退化）二次曲线$\mathrm C$上点$\mathbf x$的切线$\mathbf l$由$\mathbf l = \mathrm C \mathbf x$确定.

**证明**  因为$\mathbf l^\intercal \mathbf x = \mathbf x^\intercal \mathrm C \mathbf x = 0$, 所以直线$\mathbf l = \mathrm C \mathbf x$过$\mathbf x$. 如果$\mathbf l$仅与二次曲线交于一点，那么它就是切线，我们的证明也就完成了. 否则，假定$\mathbf l$与该二次曲线还交于另一点$\mathbf y$，则有$\mathbf y^\intercal \mathrm C \mathbf y = 0$和$\mathbf x^\intercal \mathrm C \mathbf y =\mathbf l^\intercal \mathbf y = 0$. 由此推出$(x+\alpha y)^\intercal \mathrm C (x+\alpha y)=0$对所有$\alpha$成立，这表明连接$\mathbf x$和$\mathbf y$的整条直线$\mathbf l = \mathrm C \mathbf x$在该二次曲线$\mathrm C$上，因而C是退化的（见下文）.

#### 对偶二次曲线

上面定义的二次曲线$\mathrm C$更确切地应称为**点**二次曲线，因为它定义的是点的方程. 给出了$\mathbb P^2$的对偶结论2.6以后，理所当然应该有一个由直线的方程定义的二次曲线. 这种**对偶**（或线）二次曲线也由一个$3 \times 3$矩阵表示，我们把它记为$\mathrm C^\*$. 二次曲线$\mathrm C$的切线$\mathbf l$满$\mathbf l^\intercal \mathrm C^\* \mathbf l = 0$. 其中$\mathrm C^\*$表示$\mathrm C$的伴随矩阵（伴随矩阵在附录4(p578[p404])的节A4.2(p580[406])中定义）. 对非奇异对称矩阵$\mathrm C$有$\mathrm C^\* = \mathrm C^{-1}$(相差一个尺度因子）.

当$\mathrm C$为满秩时，对偶二次曲线方程可直接推导：根据结论2.7, 过$\mathrm C$上点$\mathbf x$的切线是$\mathbf l = \mathrm C \mathbf x$. 反之，直线$\mathbf l$切于$\mathrm C$的点$\mathbf x$是$\mathbf x = \mathrm C^{-1}\mathbf l$. 因为$\mathbf x$满足$\mathbf x^\intercal \mathrm C \mathbf x = 0$, 我们得到，$(\mathrm C^{-1}\mathbf l)^\intercal \mathrm C (\mathrm C^{-1}\mathbf l)=\mathbf l^\intercal \mathrm C^{-1}\mathbf l=0$, 因为$\mathrm C$是对称的，所以$\mathrm C^{-\intercal} = \mathrm C^{-1}$, 因而得到最后的结果.

对偶二次曲线也称**二次曲线的包络**，其理由在图2.2中给予说明. 对偶二次曲线有五个自由度. 与点二次曲线相类似，一般位置上的五条线定义一条对偶二次曲线.

![image-2.2](/assets/blogImg/2.2.png)

<font size=2>**图2.2**	(a)满足$\mathbf x^\intercal \mathrm C \mathbf x = 0$的点$\mathbf x$在一条点二次曲线上.（b)满足$\mathbf l^\intercal \mathrm C^\* \mathbf l = 0$的直线$\mathbf l$是点二次曲线$\mathrm C$的切线，即二次曲线$\mathrm C$是直线$\mathbf l$的包络.

</font>

#### 退化二次曲线

非满秩矩阵$\mathrm C$所定义的二次曲线称作退化二次曲线. 退化的点二次曲线包含两条线（秩2）或一条重线（秩1).

**例2.8**  二次曲线$$\mathrm C = \mathbf l \mathbf m^\intercal + \mathbf m \mathbf l^\intercal$$由$\mathbf l$和$\mathbf m$两线组成. $\mathbf l$上的点满足$\mathbf l^\intercal \mathbf x = 0$, 因而在二次曲线上，因为$\mathbf x^\intercal \mathrm C \mathbf x=(\mathbf x^\intercal \mathbf l)(\mathbf m^\intercal \mathbf x)+(\mathbf x^\intercal \mathbf m)(\mathbf l^\intercal \mathbf x)=0$. 类似地，满足$\mathbf m^\intercal \mathbf x=0$的点同样满足$\mathbf x^\intercal \mathrm C \mathbf x = 0$. 矩阵$\mathrm C$是秩为2的对称矩阵，它的零矢量为$\mathbf x = \mathbf l \times \mathbf m$，它是$\mathbf l$和$\mathbf m$的交点.

退化的**线**二次曲线包含两个点（秩2)，或一个重点（秩1). 例如，线二次曲线$\mathrm C^\* = \mathbf x \mathbf y^\intercal + \mathbf y \mathbf x^\intercal$的秩为2并由一切过点$\mathbf x$或$\mathbf y$的直线组成. 注意对非可逆矩阵而言, $(\mathrm C^\*)^\* \neq \mathrm C$.

## 2.3  射影变换

Felix Klein在其名著：“Erlangen Program”[Klein-39]中提出：几何研究的是在变换群下保持不变的性质. 根据他的观点，2D射影几何研究的是关于射影平面$\mathbb P^2$在所谓**射影映射**的变换群下保持不变的性质.

射影映射是把$\mathbb P^2$的点（即齐次3维矢量）映射到$\mathbb P^2$的点的一种可逆映射，它把直线映射到直线. 更准确地说：

**定义2.9  射影映射**是$\mathbb P^2$到它自身的一种满足下列条件的可逆映射 $h$ : 三点$x1, x2$和$x3$共线当且仅当$h(x1), h(x2), h(x3)$也共线.

射影映射组成一个群，因为射影映射的逆以及两个射影映射的复合也是射影映射. 射影映射也称为**保线变换**(一个有益的名字)，或**射影变换**或**单应(homography)**, 它们是同义术语.

在定义2.9中，射影映射用点线关联的几何概念来定义，它与坐标无关. 基于下列定理，我们也可得到射影映射的等价的代数定义.

##### 定理2.10  映射$h$ : $\mathbb P^2 \rightarrow \mathbb P^2$是射影映射的充要条件是：存在一个$3\times3$非奇异矩阵$\mathrm H$, 使得$\mathbb P^2$的任何一个用矢量$\mathbf x$表示的点都满足$h(x)=\mathrm H \mathbf x$.

为了解释这个定理，我们用齐次3维矢量$\mathbf x$来表示$\mathbb P^2$中的点，而用$\mathrm H \mathbf x$表示齐次坐标的线性映射. 该定理断言任何射影映射都以这样一种齐次坐标的线性变换出现，反之，任何这样的映射是射影映射. 这里我们不全面地证明这个定理. 我们仅打算证明齐次坐标的任何可逆线性变换是射影映射.

**证明**  令$x1, x2$和$x3$同在一条直线$\mathbf l$上. 因此，$\mathbf l^\intercal \mathbf x_i = 0, i=1,…,3$. 令$\mathrm H$为非奇异$3\times3$矩阵. 可以证明$\mathbf l^\intercal \mathrm H^{-1} \mathrm H \mathbf x_i = 0$. 因此点$\mathrm H \mathbf x_i$都在直线$\mathrm H^{-\intercal} \mathbf l$上，因而该变换保持共线性.

其逆命题是每个射影映射都以这种方式出现，但相当难证明.

根据这个定理，可以给出射影变换(或保线变换）的另一种定义.

**定义2.11  射影变换**  平面射影变换是关于齐次3维矢量的一种线性变换，并可用一个非奇异$3\times3$矩阵$\mathrm H$表示为：$$\begin{pmatrix} x'\_1 \\\\ x'\_2 \\\\ x'\_3 \end{pmatrix} = \begin{bmatrix} h_{11}&h_{12}&h_{13} \\\\ h_{21}&h_{22}&h_{23} \\\\ h_{31}&h_{32}&h_{33} \end{bmatrix}\begin{pmatrix} x_1 \\\\ x_2 \\\\ x_3 \end{pmatrix} \tag{2.5}$$或更简洁地表示为X' = Hx.

注意 : 此方程中的矩阵$\mathrm H$乘以任意一个非零比例因子不会使射影变换改变. 换句话说$\mathrm H$是一个齐次矩阵，与点的齐次表示一样，有意义的仅仅是矩阵元素的比率. 在$\mathrm H$的九个元素中有八个独立比率，因此一个射影变换有八个自由度.

射影变换将每个图形投影为射影等价的图形，保持所有的射影性质不变. 在图2.1的射线模型中，一个射影变换就是$\mathbb R^3$的一种线性变换.

![image-2.3](/assets/blogImg/2.3.png)

<font size=2>**图2.3  中心投影把一张平面的点映射成另一张平面的点**. 过投影中心作一张与两平面$\pi$和$\pi'$相交的平面便不难看出中心投影也把直线映射为直线. 因为直线被映射为直线，所以中心投影是射影变换并可用齐次坐标的线性变换$\mathbf x' = \mathrm H \mathbf x$表示.

</font>

#### 平面之间的映射

图2.3中给出怎样应用定理2.10的一个例子. 沿过一个公共点（投影中心）的射线的投影定义了从一张平面到另一张平面的映射. 显然这种点到点的映射保持直线不变，其中一张平面上的直线被映射到另一张平面上的直线. 如果在每一张平面上建立坐标系并且采用齐次坐标来表示点，那么**中心投影**映射可以表示成$\mathbf x' = \mathrm H \mathbf x$, 其中$\mathrm H$是$3\times3$非奇异矩阵. 实际上，如果在两平面上建立的都是欧氏（直角）坐标系，那么这样一种由中心投影定义的映射比一般射影变换有更多的约束. 我们称它为**透视映射**而不是完全的射影映射，它可由一个六自由度的变换来表示. 我们将在节A5.4(p430)中再回过来讨论透视映射.

**例2.12  消除平面透视图像的射影失真.**

![image-2.4](/assets/blogImg/2.4.png)

<font size=2>**图2.4  消除透视失真**.（a)带有透视失真的原始图像——窗子的上下边线显然会聚（即延长后相交）于一个有限点.（b)合成得到的前墙的正视图. 墙的图橡(a)与墙的真实几何通过一个射影变换相联系. 逆变换是把影像得到的四个窗角的角点映射到适当大小的矩形顶点并由此计算得到. 此变换由这四组点对应确定. 再用此变换作用于整幅图像. 注意图像中的地面部分出现进一步的射影失真. 它同样可以通过一个射影变换加以消除.</font>

在透视影像下形状会失真. 例如，虽然原有的窗子是矩形，但在图2.4a中的图像上的窗子不是矩形. 景物平面上的平行线在图像上一般地不平行并会聚到一个有限点. 我们已经知道平面（或部分平面）的中心投影的图像与原平面通过射影变换相关，因而该图像是原景物的一种射影失真. 通过求该射影变换的逆变换并把它应用于图像就有可能“撤消”此射影变换. 它的结果将是一幅新的合成图像，其中在此平面上的物体将显示其正确的几何形状. 我们用图2.4a大楼的前墙对这做解释. 注意因为地面和大楼的前墙不在同一平面上，所以用于矫正大楼前墙的射影变换必然与用于地面的射影变换不一样.

我们将在第四章详细讨论由点到点的对应来求射影变换的计算. 现在，我们仅简洁地给出一种计箅该变换的方法. 首先选择世界平面与图像相对应的部分. 按图2.3所指示选择图像平面的2D局部坐标以及景物的世界坐标. 令世界与图像平面上的一对匹配点$\mathbf x$和$\mathbf x'$的非齐次坐标分别为$(x, y)^\intercal$和$(x', y')^\intercal$. 这里我们采用点的非齐次坐标而不是齐次坐标，因为从图像和世界平面的测量可以直接得到的是这些非齐次坐标.（2.5)式的射影变换可以写成如下非齐次形式：$$x'=\frac {x'\_1}{x'\_3}=\frac{h_{11}x+h_{12}y+h_{13}}{h_{31}x+h_{32}y+h_{33}},\qquad y'=\frac {x'\_2}{x'\_3}=\frac{h_{21}x+h_{22}y+h_{23}}{h_{31}x+h_{32}y+h_{33}}$$一组点对应可以提供关于$\mathrm H$元素的两个方程，把它简化后得到：$$x'(h_{31}x+h_{32}y+h_{33})=h_{11}x+h_{12}y+h_{13}$$$$y'(h_{31}x+h_{32}y+h_{33})=h_{21}x+h_{22}y+h_{23}$$

它们是关于$\mathrm H$元素的**线性**方程. 四组点对应提供八个这样的关于$\mathrm H$元素的线性方程，并足以解出$\mathrm H$(仅相差一个不重要的乘法因子）. 唯一的限制是这四点必须在“一般位置”上，即要求没有三个点共线. 用此方法计算变换$\mathrm H$并求它的逆，然后作用于整幅图像，便可消除被选平面的射影失真效应. 其结果在图2.4b中给出.

针对这个例子，我们给出三点说明：第一，用这种方法计箅矫正变换$\mathrm H$不需要知道摄像机参数或平面位置的**任何**信息；第二，为消除射影失真，并非总需要知道四个点的坐标: 节2.7将介绍另一种方法，它需要较少但不同类型的信息；第三，高级的（和推荐的）计箅射影变换的方法将在第四章中闸述.

射影变换是一种重要的映射，与世界平面的透视成像相比较，它能表示更多的情形. 图2.5中给出了其他的几个例子，我们将在本书后续的篇章中对其中的每种情形作更详尽的讨论.

![image-2.5](/assets/blogImg/2.5.png)

<font size=2>**图2.5  出现在透视图像中的几个射影变换$(\mathbf x'=\mathrm H \mathbf x)$的例子**.（a)由一张世界平面诱导的两幅图像之间的射影变换（两射影变换的复合是射影变换）；(b)摄像中心相同的两幅图像之间的射影变换（即一个摄像机绕它的中心旋转或变化它的焦距）；(c)—张平面的图像（大楼的后墙）和它的阴影在另一张平面上(地平面)的图像之间的射影变换. 图(c)取自Luc Van Gool.

</font>

### 2.3.1	直线与二次曲线的变换

#### 直线的变换

定理2.10的证明指出：如果点$\mathbf x_i$在直线$\mathbf l$上，那么经射影变换后的点$\mathbf x'_i=\mathrm H \mathbf x_i$在直线$\mathbf l'=\mathrm H^{-\intercal} \mathbf l$上. 因为$\mathbf l'^\intercal\mathbf x'_i=\mathbf l^\intercal \mathrm H^{-1}\mathrm H \mathbf x_i = 0$, 故变换$\mathrm H$保持点在直线上的性质. 它给出了关于直线的变换规则：在点变换$\mathbf x'_i=\mathrm H \mathbf x_i$下，直线$\mathbf l$变换为$$\mathbf l'=\mathrm H^{-\intercal} \mathbf l \tag{2.6}$$

我们也可以换一种写法$\mathbf l'^\intercal=\mathbf l^\intercal \mathrm H^{-1}$. 注意直线和点变换的基本区别. 点变换依据$\mathrm H$, 而直线 (视为行矢量）变换则依据$\mathrm H^{-1}$. 这可以用术语“协变”或“逆变”做解释. 我们称点变换为**逆变**而线变换为**协变**. 这种区别将在第15章讨论张量时重新提到并在附录1(p400)中全面地给予解释.

#### 二次曲线的变换

在点变换$\mathbf x'=\mathrm H \mathbf x$下，(2.2)变为$$\begin{aligned} \mathbf x^\intercal \mathrm C \mathbf x &=\mathbf x'^\intercal [\mathrm H^{-1}]^\intercal \mathrm C \mathrm H^{-1} \mathbf x' \\\\ &= \mathbf x'^\intercal \mathrm H^{-\intercal} \mathrm C \mathrm H^{-1} \mathbf x' \end{aligned}$$它是一种二次形式$\mathbf x'^\intercal \mathrm C' \mathbf x'$, 其中$\mathrm C' = \mathrm H^{-\intercal} \mathrm C \mathrm H^{-1}$. 由此得到二次曲线变换的规则：

##### 结论2.13  在点变换$\mathbf x' = \mathrm H \mathbf x$下，二次曲线$\mathrm C$变换为$\mathrm C' = \mathrm H^{-\intercal} \mathrm C \mathrm H^{-1}$.

因$\mathrm H^{-1}$出现在方程中，故可以称二次曲线变换为**协变**. 对偶二次曲线的变换规则可用类似的方式导出，即:

##### 结论2.14  在点变换$\mathbf x' = \mathrm H \mathbf x$下，对偶二次曲线$\mathrm C^{\*}$变换为$\acute{\mathrm C^{\*}} = \mathrm H \mathrm C^\* \mathrm H^{\intercal}$.

## 2.4  变换的层次

我们将在本节中介绍射影变换的重要特殊情况以及它们的几何性质. 节1.3已经指出射影变换组成一个群. 这个群被称为**射影线性群**，我们将会看到这些特殊情况都是该群的**子群**.

$n\times n$可逆实矩阵的群称为（实的）一般线性群或$GL(n)$. 当把相差非零纯量因子的矩阵都视为等同时，便得到射影线性群，记为$PL(n)$(它是$GL(n)$的商群）. 在平面射影变换时，n = 3.

$PL(3)$的重要子群包括**仿射群**和**欧氏群**，仿射群是由$PL(3)$中最后一行为(0, 0, 1)的矩阵组成的子群；欧氏群是仿射群的子群，其左上角的$2\times2$矩阵是正交的. 当左上角的$2\times2$矩阵的行列式为1时称为**定向欧氏群**.

我们将介绍这些变换，从最特殊的等距变换开始，并逐步推广直到射影变换. 由此定义了变换的一个层次. 图2.6给出属于不同层面上的各种变换的失真效果.

![image-2.6](/assets/blogImg/2.6.png)

<font size=2>**图2.6  在中心投影下出现的失真**. 花砖地板的图像. (a)**相似变换**：圆被影像为圆. 方砖被影像为正方形. 平行或垂直的线在图像中有相同的定向. (b)**仿射**：圆被影像为椭圆. 世界中的垂直线不再被影像为垂直线. 但是，在世界中的方砖的平行边在图像中仍平行. (c)**射影**: 平行世界线被影像为会聚线. 离摄像机近的方砖的图像比远的大.</font>

某些有趣的变换，例如透视映射，不是群(因为两个透视映射的复合是射影映射而不是透射映射).这方面的内容在节A5.4(p430)中讨论.

#### 不变量

介绍变换的另一种**代数方法**（即把变换视为作用于点或曲线的坐标的矩阵）是用被保持不变的元素或量即所谓的不变量来介绍. 一个几何配置的一个(标量)不变量是该配置的函数，其值在某特殊的变换下不变. 例如，两点间的距离在欧氏变换（平移和旋转）下不变，但在相似变换（即平移，旋转和均匀缩放）下则不然. 因此，距离是欧氏不变量但不是相似不变量. 两线间的夹角既是欧氏又是相似不变量.

#### 2.4.1	类 Ⅰ:  等距变换

等距(isometric)变换是平面$\mathbb R^2$的变换，它保持欧氏距离不变(*iso* = 一样，*metric* = 度量). —个等距变换可表示为$$\begin{pmatrix} x' \\\\ y' \\\\ 1 \end{pmatrix} = \begin{bmatrix} \varepsilon \cos\theta &-\sin\theta&t_x \\\\ \varepsilon \sin\theta &\cos\theta&t_y \\\\ 0&0&1 \end{bmatrix}\begin{pmatrix} x \\\\ y \\\\ 1 \end{pmatrix}$$

其中$\varepsilon = ±1$. 如果$\varepsilon$ = 1那么该等距变换是**保向**的并且也是**欧氏**变换（平移和旋转的复合）. 如果$\varepsilon=-1$, 那么该等距变换是逆向的. 例如反射（由对角矩阵出diag(-1, 1, 1)表示）与欧氏变换的复合.

欧氏变换是刚体运动的模型. 到目前为止，它们是实用中最重要的等距变换，我们将集中研究它们. 但是，保向等距变换在结构恢复时常会出现多义性.

平面欧氏变换可以用更简洁的分块形式写为$$\mathbf x'=\mathrm H_\mathrm E \mathbf x=\begin{bmatrix} \mathrm R & \mathbf t \\\\ \mathbf 0^\intercal & 1 \end{bmatrix} \mathbf x \tag{2.7}$$

其中$\mathrm R$是$2\times2$旋转矩阵（满足$\mathrm R^\intercal \mathrm R = \mathrm R \mathrm R^\intercal=\mathrm I$的正交矩阵），$\mathbf t$是2维平移矢量, 而$\mathbf 0$是2维零矢量. 特殊的情况是纯旋转(当$\mathbf t = 0$)和纯平移(当$\mathrm R=\mathrm I$). 欧氏变换也称为**位移**.

平面欧氏变换有三个自由度：旋转占一个，平移占两个. 因此，确定该变换必须指定三个参量. 该变换可以由两组点对应来计算.

#### 不变量

我们熟知它的不变量, 例如，长度（两点的距离），角度（两线的夹角）和面积.

#### 群和定向

如果等距变换左上角的$2\times2$矩阵的行列式为1，它是保向的. **保向**的等距变换形成一个群， 但**逆向**的不是. 这种区别对于下面的相似和仿射变换同样如此.

### 2.4.2 类 II:  相似变换

相似变换是一个等距变换与一个均匀缩放的复合. 当欧氏变换（即没有反射）与均匀缩放复合时，相似变换的矩阵表示为：$$\begin{pmatrix} x' \\\\ y' \\\\ 1 \end{pmatrix} = \begin{bmatrix} s \cos\theta &-s\sin\theta&t_x \\\\ s \sin\theta &s\cos\theta&t_y \\\\ 0&0&1 \end{bmatrix}\begin{pmatrix} x \\\\ y \\\\ 1 \end{pmatrix} \tag{2.8}$$可以用更简洁的分块形式写成$$\mathbf x'=\mathrm H_\mathrm S \mathbf x=\begin{bmatrix} s\mathrm R & \mathbf t \\\\ \mathbf 0^\intercal & 1 \end{bmatrix} \mathbf x \tag{2.9}$$

其中标量s表示均匀缩放. 相似变换也称**等形**变换，因为它保持了“形状”(形式). 一个平面

相似变换有四个自由度，比欧氏变换多一个缩放自由度. 相似变换可由两组点对应算出.

#### 不变量

在考虑缩放自由度影响的基础上，它的不变量可以由欧氏不变量推出. 直线的夹角不受旋转，平移或均匀缩放的影响，因而是相似不变量. 特别是平行线映射为平行线. 两点间的长度不是相似不变量, 但两长度的**比率**是不变量，因其缩放因子相互抵消. 同样地, 面积的比率是不变量，也因为缩放因子(的平方)被抵消.

#### 度量结构

讨论重构(第9章)时常用的一个术语是**度量**. 所谓**度量结构**就是确定到只相差一个相似变换的结构.

### 2.4.3 类III:  仿射变换

仿射变换是一个非奇异线性变换与一个平移变换的复合. 它的矩阵表示为$$\begin{pmatrix} x' \\\\ y' \\\\ 1 \end{pmatrix} = \begin{bmatrix} a_{11} &a_{12}&t_x \\\\ a_{21} &a_{22}&t_y \\\\ 0&0&1 \end{bmatrix}\begin{pmatrix} x \\\\ y \\\\ 1 \end{pmatrix} \tag{2.10}$$或分块形式$$\mathbf x'=\mathrm H_\mathrm A \mathbf x=\begin{bmatrix} \mathrm A & \mathbf t \\\\ \mathbf 0^\intercal & 1 \end{bmatrix} \mathbf x \tag{2.11}$$

其中$\mathrm A$是一个$2\times2$的非奇异矩阵, 平面仿射变换有六自由度，对应于六个矩阵元素. 变换可以由三组点对应来计算.

理解仿射变换中线性成分$\mathrm A$的几何效应的一个有益方法是把它看作两个基本变换——旋转和非均匀缩放的复合. 仿射矩阵$\mathrm A$总能分解为$$\mathrm A = \mathrm R(\theta)\mathrm R(-\phi)\mathrm D\mathrm R(\phi) \tag{2.12}$$其中$\mathrm R(\theta)$和$\mathrm R(\phi)$分别表示转角为$\theta$和$\phi$的旋转，而$\mathrm D$为对角矩阵：$$\mathrm D=\begin{bmatrix} \lambda_1 & 0 \\\\ 0 & \lambda_2 \end{bmatrix}$$分解式(2.12)由SVD(节A3.3(p408))(SVD为“奇异值分解”的缩写)直接得到: $\mathrm A=\mathrm U\mathrm D\mathrm V^\intercal=(\mathrm U\mathrm V^\intercal)(\mathrm V\mathrm D\mathrm V^\intercal) = R(\theta)(R(-\phi)\mathrm D\mathrm R(\phi))$, 其中第一个等式来自$\mathrm A$的SVD, 其他 等式则因为$\mathrm U$和$\mathrm V$是正交矩阵.

因此，仿射矩阵$\mathrm A$被看成是一个旋转($\phi$);加上在(已旋转)的$x$和$y$方向分别进行按比例因子$\lambda_1$和$\lambda_2$的缩放，再加上一个回转($-\phi$)和最后一个旋转($\theta$)的复合变换. 与相似变换相比，“新”几何仅仅是非均匀缩放. 它使仿射变换比相似变换多了两个自由度. 它们是缩放方向的角度($\phi$)和缩放参数比率$\lambda_1:\lambda_2$. 仿射变换的本质是在一个特定角的两个垂直方向上进行缩放. 图2.7给出两个示意性例子.

![image-2.7](/assets/blogImg/2.7.png)

<font size=2>**图2.7  平面仿射变换失真**. (a)旋转R($\theta$). (b)形变R($-\phi$)DR($\phi$). 注意形变中缩放方向是正交的.

</font>

#### 不变量

因为仿射变换包含非均匀缩放，所以长度比率和线间夹角等相似不变量在仿射变换下不再保留. 三个重要的仿射不变量是：

1. **平行线**  考査两平行线. 它们交于某无穷远点$(x1, x2, 0)^\intercal$. 在仿射变换下，该点被映射到另一个无穷远点. 因此，平行线被映射到仍然交于无穷远的直线，即它们的像仍然平行.
2. **平行线段的长度比**  直线段的长度缩放仅与该线段方向和缩放方向之间的夹角有关. 假定该线段与正交缩放方向$x-$轴的夹角为$\alpha$, 那么，缩放大小为$\sqrt{\lambda_1^2\cos^2\alpha + \lambda_2^2\sin^2\alpha}$. 因该缩放因子对所有同向的直线是一样的，所以在平行线段的比率中被消去.
3. **面积比**  可以通过分解式(2.12)直接得到该不变性. 旋转和平移不影响面积，起作用的仅仅是按$\lambda_1$和$\lambda_2$的缩放. 其效果是面积被缩放了$\lambda_1\lambda_2=\mathrm d\mathrm e\mathrm t\;\mathrm A$倍. 因此，任何形状的面积都被缩放$\mathrm d\mathrm e\mathrm t\;\mathrm A$倍，而该缩放因子在面积比中被消去. 我们将会看到这一性质在射影变换时不成立.

根据det A是正或负，仿射变换分别称为保向的或逆向的. 因$\mathrm d\mathrm e\mathrm t\;\mathrm A=\lambda_1\lambda_2$，保向性或逆向性仅与其符号有关.

### 2.4.4类 Ⅳ: 射影变换

射影变换已在(2.5)中定义. 它是**齐次**坐标的一般非奇异线性变换. 它是仿射变换的推广，仿射变换是**非齐次**坐标的一般非奇异线性变换和一个平移的复合. 我们早已见到射影变换的作用(节2.3). 这里，我们取其分块形式$$\mathbf x'=\mathrm H_\mathrm P \mathbf x=\begin{bmatrix} \mathrm A & \mathbf t \\\\ \mathbf v^\intercal & v \end{bmatrix} \mathbf x \tag{2.13}$$

其中矢量$\mathbf v= (v_1, v_2)^\intercal$. 齐次矩阵虽有九个元素但只有它们的比率是有意义的，因此该变换由八个参数确定. 注意并不是总有可能通过对矩阵缩放而取$v$为1，因为$v$可能是零. 两平面之间的射影变换可由四组点对应算出，但其中属于同一平面的三点必须不共线. 见图2.4.

与仿射变换不同，在$\mathbb P^2$中不能区分保向或逆向射影变换. 我们将在节2.6中回到这个问题.

#### 不变量

最基本的射影不变量是四共线点的交比：直线上长度的比率在仿射变换下保持不变，但在射影变换下并非如此. 然而比率的比率即直线上长度的**交比**是射影不变量. 我们将在节2.5讨论这个不变量的性质.

#### 2.4.5  小结与比较

仿射变换(6 dof)介于相似变换(4 dof)与射影变换(8 dof)之间. 仿射变换推广相似变换使得夹角不再保持不变，造成物体形状在变换后产生歪斜. 另一方面，仿射变换对平面的作用是均匀的：对于一个给定的仿射变换，平面上任何地方的物体(比如说一个正方形)的面积缩放因子det A都是一样的；直线变换的方向取决于它原来的方向，而与它在平面上的位置无关. 与此相反，经一个给定的射影变换后，面积的缩放随位置的改变而改变(比如在透视变换下，平面上较远的正方形比较近的正方形的图像小，如图2.6所示)；并且直线变换的方向既取决于原线的方向又取决于它的位置(在节7.6(p149)中将会看到线的消影点仅取决于直线的方向，与位置无关).

射影与仿射变换的根本区别在于射影变换中矢量$\mathbf v$不是零. 由它引起射影变换的非线性效应. 把理想点$(x1, x2, 0)^\intercal$在仿射和射影变换下的映射做一个对比. 首先看仿射变换，$$ \begin{bmatrix} \mathrm A &\mathbf t \\\\ \mathbf 0^\intercal&1 \end{bmatrix}\begin{pmatrix} x_1 \\\\ x_2 \\\\ 0 \end{pmatrix}=\begin{pmatrix} \mathrm A \begin{pmatrix} x_1 \\\\ x_2 \end{pmatrix}  \\\\ 0 \end{pmatrix} \tag{2.14}$$再看，射影变换$$ \begin{bmatrix} \mathrm A &\mathbf t \\\\ \mathbf v^\intercal&v \end{bmatrix}\begin{pmatrix} x_1 \\\\ x_2 \\\\ 0 \end{pmatrix}=\begin{pmatrix} \mathrm A \begin{pmatrix} x_1 \\\\ x_2 \end{pmatrix}  \\\\ v_1x_1+v_2x_2 \end{pmatrix} \tag{2.15}$$

在第一种情况下，理想点仍然是理想点(在无穷远处). 在第二种情况下，理想点被映射到有 限点. 正是因为具有这种能力，射影变换能对消影点建模.

#### 2.4.6  射影变换的分解

射影变换可以分解为一串变换链的复合，链中的每个矩阵比它前面的一个矩阵所表示的变换层次高.$$\mathrm H=\mathrm H_\mathrm S \mathrm H_\mathrm A \mathrm H_\mathrm P=\begin{bmatrix} s\mathrm R & \mathbf t/v \\\\ \mathbf 0^\intercal &1 \end{bmatrix}\begin{bmatrix} \mathrm K & \mathbf 0 \\\\ \mathbf 0^\intercal &1 \end{bmatrix}\begin{bmatrix} \mathrm I & \mathbf 0 \\\\ \mathbf v^\intercal &v \end{bmatrix}=\begin{bmatrix} \mathrm A & \mathbf t \\\\ \mathbf v^\intercal &v \end{bmatrix} \tag{2.16}$$

其中$\mathrm A=s\mathrm R\mathrm K + \mathbf t\mathbf v^\intercal/v$, 而$\mathrm K$是满足det K = 1的归一化上三角矩阵. 如果$v\neq0$上述分解是有效的，而且如果 $s$ 取正值，它还是唯一的.

矩阵$\mathrm H_\mathrm S、\mathrm A_\mathrm A、\mathrm H_\mathrm P$分别是相似变换、仿射变换和射影变换(如下标S、A、P所指示). 考察例2.12中对平面的透视图像进行矫正的过程：$\mathrm H_\mathrm P(2\;\mathrm d\mathrm o\mathrm f)$移动无穷远直线；$\mathrm H_\mathrm A(2\;\mathrm d\mathrm o\mathrm f)$影响仿射性质，而不移动无穷远直线；最后，$\mathrm H_\mathrm S$是一般的相似变换(4 dof)，它不影响仿射及射影性质. 变换$\mathrm H_\mathrm P$属于节A5.3(p430)所介绍的一种有约束的透视变换.

**例2.15**  射影变换

$$\mathrm H=\begin{bmatrix} 1.707 & 0.586 &1.0 \\\\ 2.707 &8.242&2.0 \\\\ 1.0 & 2.0 &1.0 \end{bmatrix}$$可以分解为$$\mathrm H=\begin{bmatrix} 2\cos45° & -2\sin45° &1 \\\\ 2\sin 45° &2\cos45°&2 \\\\ 0 & 0 &1 \end{bmatrix}\begin{bmatrix} 0.5 & 1 &0 \\\\ 0 &2&0 \\\\ 0 & 0 &1 \end{bmatrix}\begin{bmatrix} 1 & 0 &0 \\\\ 0 &1&0 \\\\ 1 & 2 &1 \end{bmatrix}$$

当我们的目标只是确定部分的变换时，可以应用这样的分解. 例如，如果我们需要从平面的 射影图像中测量长度比，那么仅需要确定(矫正)到相似变换. 我们将在节2.7回过来讨论这种方法.

#### 2.4.7 不变量的数目

已知几何配置，在一种特定类型的变换下不变量的数目是多少？首先术语“数目”需要进一步精确化，如果一个量是不变量，例如欧氏变换下的长度，那么该量的任何函数也是不变量. 因而，我们要寻求一个与函数无关的不变量计数法则. 为了形成不变量就必须消去变换矩阵的参数数目，考虑到这点后可以导出：

##### 结论2.16  与函数无关的不变量数等于或大于配置的自由度数减去变换的自由度数.

例如，由一般位置上的四点所组成的配置有八个自由度(每点2个)，从而有4个相似，2个仿射和零个射影不变量，因为这些变换分别有4,6,8自由度.

表2.1归纳了2D变换群以及它们的不变性质. 在表中低层的变换是它高层的变换的特殊化. 在表低层的变换继承其高层变换的不变量.

![table-2.1](/assets/blogImg/t2.1.png)

<font size=2>**表2.1.  常见平面变换的几何不变性质**  $\mathrm A = \begin{bmatrix} a_{ij} \end{bmatrix}$是$2\times2$的可逆矩阵，$\mathrm R = \begin{bmatrix} r_{ij} \end{bmatrix}$是2D旋转矩阵，$(t_x, t_y)^\intercal$是2D平移矢量. 失真列给出变换对正方形所产生的典型效应. 表中高层的变换可以产生比它低层的变换的所有效应. 它们的范围从欧式(其中仅有平移和旋转)到射影(其中正方形被变换成任意四边形(假定没有三点共线)).

</font>

## 2.5  1D射影几何

直线射影几何$\mathbb P^1$的推导方法与平面的几乎一样. 直线上的点 $x$ 用齐次坐标表示为$(x1, x2)^\intercal$，而$(x1, 0)^\intercal$是该直线的理想点. 我们将用记号$\overline{\mathbf x}$表示2维矢量$(x_1, x_2)^\intercal$. 直线的射影变换由一个$2\times2$的齐次矩阵来表示：$$\overline {\mathbf x}'=\mathrm H_{2\times2}\overline {\mathbf x}$$它有3个自由度，即矩阵的四个元素减去一个全局缩放因子. 直线的射影变换可以由3组对应点来确定.

##### 交比

交比是$\mathbb P^1$的基本射影不变量. 给定4个点$\overline {\mathbf x}\_i$，**交比**定义为$$\rm Cross(\overline {\mathbf x}\_1, \overline {\mathbf x}\_2, \overline {\mathbf x}\_3, \overline {\mathbf x}\_4)=\frac{\left| \overline {\mathbf x}\_1\overline {\mathbf x}\_2 \right| \left| \overline {\mathbf x}\_3\overline {\mathbf x}\_4 \right|}{\left| \overline {\mathbf x}\_1\overline {\mathbf x}\_3 \right| \left| \overline {\mathbf x}\_2\overline {\mathbf x}\_4 \right|}$$其中$$\left| \overline {\mathbf x}\_i\overline {\mathbf x}\_j \right|=\rm det \begin{bmatrix} x_{i1} & x_{j1} \\\\ x_{i2}&x_{j2} \end{bmatrix}$$

关于交比的几点注释如下：

1. 交比的值与各点$\overline {\mathbf x}_i$所用的具体的齐次表示无关，因为分子和分母的缩放因子在它们之间抵消.
2. 如果每点$\overline {\mathbf x}_i$都是有限点，并在齐次表示中均选择$x_2 = 1$, 那么$\left| \overline {\mathbf x}_i\overline {\mathbf x}_j \right|$就表示由到$\overline {\mathbf x}_i$到$\overline {\mathbf x}_j$的带符号的距离.
3. 如果点$\overline {\mathbf x}_i$中有一个理想点，交比的定义仍然有效.
4. 在任何直线的射影变换下，交比的值不变：如果$\overline {\mathbf x}'= \mathrm H_{2\times2}\overline {\mathbf x}$则$$\rm Cross(\overline {\mathbf x}_1', \overline {\mathbf x}_2', \overline {\mathbf x}_3', \overline {\mathbf x}_4') = Cross(\overline {\mathbf x}_1, \overline {\mathbf x}_2, \overline {\mathbf x}_3, \overline {\mathbf x}_4) \tag{2.17}$$

以上四点的证明留着习题. 它也可以说成是：交比对于直线的射影坐标选择是不变量. 图2.8说明若干等交比的直线之间的射影变换.

平面射影变换下，平面上的任何直线都诱导一个1D射影变换.

![image-2.8image-2.8](/assets/blogImg/2.8.png)

<font size=2>**图2.8  直线之间的射影变换**. 图中有四组四共线点. 每组与其他组都通过直线到直线的射影变换相关联. 因为在射影变换下交比是不变量. 所以图中每一组有相同的交比值.</font>

#### 共点线

共点线是直线上共线点的对偶. 这意味着平面上的共点线也有几何$\mathbb P^1$. 特别是, 任何四条共点线都有一个确定的交比，如图2.9a所示.

![image-2.9](/assets/blogImg/2.9.png)

<font size=2>**图2.9  共点直线**.  (a)四共点线$\mathbf l_i$与直线$\mathbf l$交于四点$\overline {\mathbf x}_i$. 这些直线的交比对平面射影变换来说是不变量. 它的值由点的交比：$\rm Cross(\overline {\mathbf x}_1, \overline {\mathbf x}_2, \overline {\mathbf x}_3, \overline {\mathbf x}_4)$给出. (b)共面点$\mathbf x_i$被过中心$\mathbf c$的投影影像到一直线$\mathbf l$(也在该平面上). 像点$\overline {\mathbf x}_i$的交比对像线$\mathbf l$的位罝来说是不变量.

<font>

请注意图2.9b如何把平面$\mathbb P^2$上的点投影到1维图像的表示方法. 特别是，如果$\mathbf c$表示摄像机中心，而直线$\mathbf l$表示像直线(像平面的1D情况)，那么点$\overline {\mathbf x}_i$是点$\mathbf x_i$向该像直线的投影. 点$\overline {\mathbf x}_i$的交比刻画四个像点的射影配置. 注意，就四个像点的射影配置而论，像直线的实际位置是无关紧要的——不同像直线的选择都给出射影等价的像点配置.

共点线的射影几何对理解第8章中对极线的射影几何非常重要.

## 2.6  射影平面的拓扑

本节中**简短地介绍一下$\mathbb P^2$的拓扑. 它并非是理解本书以后的内容所必须的.**

我们已经知道射影平面$\mathbb P^2$可以视为全体齐次3维矢量的集合. 这种类型的矢量$\mathbf x=(x_1, x_2, x_3)^\intercal$可以通过乘以一个非零因子使得$x_1^2+x_2^2+x_3^2=1$来归一化. 这样的一个点在$\mathbb R^3$的单位球面上. 但在$\mathbb P^2$中，任何相差一个乘数因子-1的矢量x和-x表示同一点. 因此，$\mathbb R^3$的单位球面$S^3$和射影平面$\mathbb P^2$之间存在一种二对一的对应. 射影平面可以画成一单位球面，其中符号相反的点视为等同. 在此表示中，$\mathbb P^2$上的直线被模型化为单位球面上的大圆( 符号相反的正对点仍等同 ). 可以验证，球上任何两不同的( 非正对 )点恰好在一大圆上，任何两大圆相交于一点( 因为正对点等同 ).

按拓扑学的语言，球面$S^2$是$\mathbb P^2$的2-叶复盖空间. 从而$\mathbb P^2$不是**单连通**的，即在$\mathbb P^2$中存在环，不能在$\mathbb P^2$内收缩到一点. 更专业化地说，$\mathbb P^2$的基本群是阶为2的循环群.

在把射影平面看作正对点等同的球面的模型中，可以把球而$S^2$的下半球拿掉，因为下半球的点与其在上半球的正对点一样. 在这种情形下，$\mathbb P^2$可以由上半球面构成并认为在赤道上正对点等同. 因为$S^2$的上半球拓扑等价于一个圆盘，所以$\mathbb P^2$就是一个圆盘而且边缘上正对点被视为等同或粘在一起. 这在物理上是不可能的. 通过给圆盘粘边界来构造拓扑空间是拓扑学常用的方法，并且事实上，任何2维流形都可以用这方法来构造，如图2.10所示.

![image-2.10](/assets/blogImg/2.10.png)

<font size=2>**图2.10  表面的拓扑**.  把正方形纸片( 拓扑等价于圆盘 )的边适当相粘接可以构成通常的曲面. 在每种情况中, 把正方形中有相同箭头的两边粘在一起并保持箭头的方叫一致. 我们得到( a )球面( b )环面( c )Klein 瓶( d )射影平面. 只有球面和环面可用带箭头的纸真正实现. 球面和环面是可定向的但射影平面和Klein瓶则不然.

</font>

射影平面$\mathbb P^2$的一个显著特点是不可定向，即不可能定义一个在整个表面上保持一致的局部方位( 例如由一对有向坐标轴来表示 ). 图2.11对此给予了说明，即证明射影平面包含一条逆向的路径.

![image-2.11](/assets/blogImg/2.11.png)

<font size=2>**图2.11  曲面的定向**.  一个坐标系( 图中用L表示 )可以沿曲面上—条路径平移并最终回到它的出发点. (a)表示一个射影平面. 坐标系( 由一对轴表示 )沿给出的路径回到出发点时被反向了，因为正方形的边界上视为等同的两个对点有一条轴的方向调转了. 这样的路径称为逆向路径，含有逆向路径的曲面称不可定向的. (b)给出一个熟知的例子：麦比乌斯带, 它由反向粘接长方形的两对边而得到. 可以验证，绕麦比乌斯带一圈的路径是逆向的.

</font>

#### $\mathbb P^1$的拓扑

类似地，1维射影线可以等同于1维球面$S^1$( 即圆 )且正对点视为等同. 如果去掉可由上半圆复制的下半圆，那么上半圆便拓扑等价于一直线段. 因此$\mathbb P^1$拓扑等价于一条将两端视为等同的直线段，它也拓扑等价于圆$S^1$.

## 2.7  从图像恢复仿射和度量性质

我们回到射影矫正的例子(例2.12(p9)), 它的目的是消除平面的透视图像中的射影失真，使得原始平面的相似性质( 角度，长度比 )可以被测量. 在该例中，通过确认平面上四个参考点的位置( 共8个自由度 )，并显式地算出映射参考点到它们图像的变换，射影失真被完全消除. 事实上，它超定了该几何——射影变换仅比相似变换多4个自由度，因此，为确定度量性质仅需要指定4个自由度( 不是8 ). 在射影变换中，这4个自由度给出与几何对象相关联的“物理本质”：无穷远直线$\mathbf l_\infty$(2dof)和在$\mathbf l_\infty$上的两个**虚圆点**(2dof). 这种关联性用于此问题的推理通常比分解链(2.16)中具体矩阵的描述更直观，虽然它们是等价描述.

下文将证明一旦$\mathbf l_\infty$的像被指定，射影失真便可消除，而一旦虚圆点被指定，仿射失真也可消除. 然后，余下的只是相似失真.

### 2.7.1	无穷远线

在射影变换下，理想点可以映射为有限点(2.15), 因而$\mathbf l_\infty$被映射为有限直线. 但如果是仿射变换，$\mathbf l_\infty$不会被映射为有限直线，即仍留在无穷远处. 显然，它可直接由直线的变换(2.6 - p10)推出：$$\mathbf l_\infty'=\mathrm H_\mathrm A^{-\intercal}\mathbf l_\infty= \begin{bmatrix} \mathrm A^{-\intercal} &\mathbf 0 \\\\ -\mathbf t^\intercal\mathrm A^{-\intercal} &\mathbf 1 \end{bmatrix}\begin{pmatrix} 0 \\\\ 0 \\\\ 1 \end{pmatrix}=\begin{pmatrix} 0 \\\\ 0 \\\\ 1 \end{pmatrix} =\mathbf l_\infty$$逆命题也是正确的，即仿射变换是保持$\mathbf l_\infty$不变的最一般的线性变换，并可证明如下. 我们要求一个无穷远点，例如$\mathbf x=(1,0,0)^\intercal$, 被映为一个无穷远点. 该要求就决定了$h_{31} =0$,同理可证$h_{32}=0$,所以该变换是仿射变换. 概括起来：

##### 结论2.17  在射影变换$\mathrm H$下，无穷远直线$\mathbf l_\infty$为不动直线的充要条件是$\mathrm H$是仿射变换.

但是，在仿射变换下, $\mathbf l_\infty$不是点点不动的：(1.14)表明在仿射变换下$\mathbf l_\infty$的点( 理想点 )被映射为$\mathbf l_\infty$的点，但它不是原来的点，除非$\mathrm A(x_1, x_2)^\intercal=k(x_1, x_2)^\intercal$. 现在可以证明辨认$\mathbf l_\infty$就能恢复仿射性质(平行，面积比).

### 2.7.2	由图像恢复仿射性质

在平面的像中，一旦无穷远直线的像得到辨认，就有可能对原平面进行仿射测量. 例如，如果两条直线的影像相交在$\mathbf l_\infty$的像上，则可认定这两条直线在原平面上平行. 这是因为在欧氏平面中平行线相交在$\mathbf l_\infty$上，又因射影变换保持交点不变，经射影变换之后直线仍然交于$\mathbf l_\infty$的像上. 类似地，一旦$\mathbf l_\infty$被辨认，直线上的长度比率便可由像平面上该直线上确定长度的三个点以及该直线与$\mathbf l_\infty$的交点( 它提供交比的第四点 )的交比来计算，等等.

但是，一个转弯不大却更适合于计算机算法的途径是直接把已辨认的$\mathbf l_\infty$变换到它的规范位置$\mathbf l_\infty=(0, 0, 1)^\intercal$. 把实现此变换的(射影)矩阵应用于图像中的每一点以达到对图像进行仿射矫正的目的，即变换之后，仿射测量可以直接在矫正过的图像中进行. 这个基本思想在图2.12中加以说明.

![image-2.12](/assets/blogImg/2.12.png)

<font size=2>**图2.12  仿射矫正**.  射影变换把$\mathbf l_\infty$从欧氏平面$\pi_1$的$(0, 0, 1)^\intercal$映射到平面$\pi_2$的有限直线$\mathbf l$. 如果构造一个射影变换把$\mathbf l$映射回$(0, 0, 1)^\intercal$,那么根据结论2.17从第一到第三张平面的变换必定是仿射变换. 因为$\mathbf l_\infty$的标准位置保持不变. 这意味着第一张平而的仿射性质可以从第三张平面上测量，即第三张平面是第一平面的仿射像.

</font>

如果无穷远直线的像是$\mathbf l=(l_1, l_2, l_3)^\intercal$，假定$l_3\neq0$,那么把$\mathbf l$映射回$\mathbf l_\infty=(0, 0, 1)^\intercal$的一个合适的射影点变换是$$\mathrm H=\mathrm H_\mathrm A\begin{bmatrix} 1&0&0 \\\\0&1&0\\\\l_1&l_2&l_3\end{bmatrix} \tag{2.18}$$其中$\mathrm H_\mathrm A$可取为任何仿射变换( $\mathrm H$的最后一行是$\mathbf l^\intercal$). 可以验证在直线变换(1.6 - p10)下, $\mathrm H^{-\intercal}(l_1, l_2, l_3)^\intercal=(0,0,1)^\intercal=\mathbf l_\infty$.

##### 例2.18  仿射矫正

![image-2.13](/assets/blogImg/2.13.png)

<font size=2>**图2.13  通过消影线实现仿射矫正**.  (a)中平面的消影线在(c)中用两组平行线的像的交点计算. 此后, 图像(a)经过射影形变生成仿射矫正图像(b). 被仿射矫正的图像中平行线的像现在平行的. 但是, 夹角不再是它们在世界平面上的直角, 因为它们被仿射失真了. 同时参见图2.17.

</font>

在平面的透视图像中，世界平面上的无穷远直线被影像为该平面的消影线. 第7章中将对它作更详细的讨论. 如图2.13所示，消影线$\mathbf l$可以由平行线的影像的交点来计算. 然后用射影形变(2.18)作用于图像平面，使$\mathbf l$映射到它的规范位置$\mathbf l_\infty =(0, 0, 1)^\intercal$，从而达到图像仿射矫正的目的.

该例表明仿射性质可以通过指定一条直线(2dof)来恢复. 这等价于仅仅指定变换分解链(2.16)中的射影成分. 相反，如果已知仿射属性，则可以使用这些属性来确定无穷远点和无穷远线。以下示例说明了这一点。

##### 例2.19  从长度比计算消影点

给定一条直线上有已知长度比的两个线段，该直线上的无穷远点便可以确定. 典型的情况是在像直线上的三个点$\mathbf a', \mathbf b', \mathbf c'$已被辨认. 假定$\mathbf a, \mathbf b, \mathbf c$是世界直线上对应的共线点，且长度比$d(\mathbf a, \mathbf b):d(\mathbf b, \mathbf c)=a:b$已知( 这里$d(\mathbf x, \mathbf y)$是点$\mathbf x$和$\mathbf y$之间的欧氏距离 ). 有可能利用交比找到消影点. 其过程如下：
1. 在图像中量出距离比$d(\mathbf a', \mathbf b'):d(\mathbf b', \mathbf c')=a':b'$.
2. 在直线$<\mathbf a, \mathbf b, \mathbf c>$上建立坐标系，使点$\mathbf a, \mathbf b, \mathbf c$的坐标分别为$0, a, a+b$. 为计算方便,把这些点表示为齐次2维矢量$(0, 1)^\intercal$, $(a, 1)^\intercal$和$(a+b, 1)^\intercal$. 类似地，令$\mathbf a', \mathbf b', \mathbf c'$的坐标为$0, a', a'+b'$，并且它们同样可表示为齐次矢量.
3. 相对于这些坐标系，计算使$\mathbf a \mapsto \mathbf a', \mathbf b \mapsto \mathbf b', \mathbf c \mapsto \mathbf c'$的1D射影变换$\mathrm H_{2\times2}$.
4. 在变换$\mathrm H_{2\times2}$下无穷远点的像( 坐标为$(1, 0)^\intercal$ )是直线$<\mathbf a', \mathbf b', \mathbf c'>$的消影点. 

用这种方式计算消影点的例子在图2.14中给出.

![image-2.14](/assets/blogImg/2.14.png)

<font size=2>图2.14  用直线上等距比来确定无穷远点的两个例子. 被采用的线段用细和粗的白线（以点界定）标记.这种作图方法确定了平而的消影线. 把它与图2.13c作比较.

</font>

##### 例2.20  由长度比作消影点的几何作图

图2.14中给出的消影点也可以用纯几何作图的方法得到，步骤如下：

1. 给定：图像中三共线点$\mathbf a', \mathbf b', \mathbf c'$，它们与线段比是$a:b$的世界共线点对应.
2. 过$\mathbf a'$画任意直线$\mathbf l$(不与直线$\mathbf a'\mathbf c'$重叠)并标注点$\mathbf a = \mathbf a', \mathbf b, \mathbf c$使线段$<\mathbf a\mathbf b>, <\mathbf b\mathbf c>$的长度比为$a:b$.
3. 连接$\mathbf b\mathbf b'$和$\mathbf c\mathbf c'$,它们交于$\mathbf o$.
4. 过$\mathbf o$作平行$\mathbf l$的直线交直线$\mathbf a'\mathbf c'$于消影点$\mathbf v'$.

该作图过程在图2.15中说明.

![image-2.15](/assets/blogImg/2.15.png)

<font size=2>图2.15  已知长度比，求一直线上无穷远点的像的几何作图法. 细节在正文中给出.</font>

### 2.7.3  虚圆点及其对偶

在相似变换下，$\mathbf l_\infty$上有两个不动点. 它们是**虚圆点**(也称**绝对点**)$\mathbf I, \mathbf J$,其标准坐标是$$\mathbf I=\begin{pmatrix} 1\\\\ i \\\\0 \end{pmatrix} \qquad \mathbf J= \begin{pmatrix} 1 \\\\ -i \\\\ 0 \end{pmatrix}$$这一对虚圆点是复共轭理想点. 它们在保向相似变换下不变:$$\begin{aligned} \mathbf I' &= \mathrm H_\mathrm S \mathbf I\\\\ &= \begin{bmatrix} s\cos\theta & -s\sin\theta & t_x \\\\ s\sin\theta & s\cos\theta & t_y \\\\ 0&0&1 \end{bmatrix} \begin{pmatrix} 1\\\\ i \\\\0 \end{pmatrix}\\\\ &= se^{-i\theta} \begin{pmatrix} 1\\\\ i \\\\0 \end{pmatrix}=\mathbf I \end{aligned}$$

类似地，可以给出$\mathbf J$的证明. 一个反射变换使$\mathbf I$和$\mathbf J$交换. 逆命题也成立，即如果虚圆点在一个线性变换下不动，那么该线性变换必是相似变换，其证明留作练习. 槪括起来有：

##### 结论2.20. 在射影变换$\mathrm H$下, 虚圆点$\mathbf I$和$\mathbf J$为不动点的充要条件是$\mathrm H$是相似变换.

“虚圆点”的命名起源于每一圆周交$\mathbf l_\infty$于虚圆点. 为了证明这一点，由二次曲线方程(2.1-p6)开始. 在二次曲线为圆时有$a=c$且$b=0$. 则$$x_1^2+x_2^2+dx_1x_3+ex_2x_3+fx_3^2=0$$其中$a$取为1. 该二次曲线交$\mathbf l_\infty$于(理想)点$(x_3=0)$，即$$x_1^2+x_2^2=0$$解得，$\mathbf I=(1, i, 0)^\intercal, \mathbf J=(1, -i, 0)^\intercal$, 即任何圆都交$\mathbf l_\infty$于虚圆点. 在欧氏几何中我们知道一个圆由三点指定. 虚圆点引出另一种计算. 圆可以用由五个点定义的一般二次曲线的公式(2.4 - p6)来计算，它的五个点是三个点加上两个虚圆点. 节2.7.5将证明辨认虚圆点(等价地辨认它们的对偶，见下文)能够恢复相似性质（角度，长度比）. 代数上，虚圆点是把欧氏几何中两正交方向$(1, 0, 0)^\intercal, (0, 1, 0)^\intercal$合并到一个复共轭中，即$$\mathbf I=(1, 0, 0)^\intercal+i(0, 1, 0)^\intercal$$

因此，不足为奇，一旦虚圆点被辨认，正交性和其他的度量性质就可以被确定.

#### 与虚圆点对偶的二次曲线

二次曲线$$\mathrm C^\*_\infty=\mathbf I\mathbf J^\intercal +\mathbf J\mathbf I^\intercal \tag{2.19}$$

与虚圆点对偶. $\mathrm C_\infty^\*$是由这两个虚圆点构成的退化(秩为2)的线二次曲线(见节2.2.3). 在欧氏坐标系下写为$$\mathrm C_\infty^\*=\begin{pmatrix} 1\\\\i\\\\0 \end{pmatrix}\begin{pmatrix} 1&-i&0 \end{pmatrix}+\begin{pmatrix} 1\\\\-i\\\\0 \end{pmatrix}\begin{pmatrix} 1&i&0 \end{pmatrix}\begin{bmatrix} 1&0&0\\\\0&1&0\\\\0&0&0 \end{bmatrix}$$

类似于虚圆点的不动性质，二次曲线$\mathrm C_\infty^\*$在相似变换下不变. 在某变换下，二次曲线的矩阵如果不变(相差一常数)，则称该二次曲线在此变换下不变. 因为$\mathrm C_\infty^\*$是对偶二次曲线，它的变换遵循结论2.14(p11)$(\mathrm C^{\*'} = \mathrm H\mathrm C^\* \mathrm H^\intercal)$,可以验证在点相似变换$\mathbf x' = \mathrm H_\mathrm S\mathbf x$下，$$\mathrm C_\infty^{\*'} = \mathrm H_\mathrm S\mathrm C_\infty^\* \mathrm H_\mathrm S^\intercal=\mathrm C_\infty^\*$$其逆命题也正确，从而得到

##### 结论2.21  对偶二次曲线$\mathrm C_\infty^\* $在射影变换$\mathrm H$下不变的充要条件是$\mathrm H$是相似变换.

在任何射影框架下, $\mathrm C_\infty^\* $所具有的一些性质：

1. $\mathrm C_\infty^\* $有4自由度：$3\times3$齐次对称矩阵有5个自由度，但约束det $\mathrm C_\infty^\* $ = 0减去一个自由度.
2. $\mathbf l_\infty$是$\mathrm C_\infty^\* $的零矢量. 根据定义虚圆点在$\mathbf l_\infty$上，即$\mathbf I^\intercal \mathbf l_\infty=\mathbf J^\intercal\mathbf l_\infty=0$, 从而$$\mathrm C_\infty^\* \mathbf l_\infty = (\mathbf I\mathbf J^\intercal + \mathbf J\mathbf I^\intercal)\mathbf l_\infty =\mathbf I(\mathbf J^\intercal \mathbf l_\infty) +\mathbf J(\mathbf I^\intercal \mathbf l_\infty) = 0$$

### 2.7.4	射影平面上的夹角

在欧氏几何中，两线间的夹角由它们法线的点乘来计算. 直线$\mathbf l=(l_1, l_2, l_3)^\intercal$和$\mathbf m=(m_1, m_2, m_3)^\intercal$的法线分别平行于$(l_1, l_2)^\intercal$和$(m_1, m_2)^\intercal$，其夹角为$$\cos\theta=\frac{l_1m_1+l_2m_2}{\sqrt{(l_1^2+l_2^2)(m_1^2+m_2^2)}} \tag{2.20}$$

平面经仿射或射影变换后，公式(2.20)不能被使用，问题出在$\mathbf l$和$\mathbf m$的头两个分量在射影变换下没有良定的变换性质( 它们不是张量 ), 但类似于(2.20)在射影变换下不变的公式为$$\cos\theta=\frac{\mathbf l^\intercal \mathrm C_\infty^\* \mathbf m}{\sqrt{(\mathbf l^\intercal \mathrm C_\infty^\* \mathbf l)(\mathbf m^\intercal \mathrm C_\infty^\* \mathbf m)}} \tag{2.21}$$

其中$\mathrm C_\infty^\* $是与虚圆点对偶的二次曲线. 不言而喻，在欧氏坐标系下，(2.21)被简化为(2.20).

在点变换$\mathbf x' = \mathrm H_\mathrm S\mathbf x$下，采用直线(2.6 - p10)$(\mathbf l' = \mathrm H^{-\intercal} \mathbf l)$和对偶二次曲线(2. 14(p11))$(\mathrm C^{\*'} = \mathrm H\mathrm C^\* \mathrm H^\intercal)$的变换规则，可以证明(2.21)在射影变换下不变. 例如，分子变换为：$$\mathbf l^\intercal \mathrm C_\infty^\* \mathbf m\mapsto \mathbf l^\intercal \mathrm H^{-1}\mathrm H\mathrm C_\infty^\* \mathrm H^\intercal \mathrm H^{-\intercal}\mathbf m=\mathbf l^\intercal \mathrm C_\infty^\* \mathbf m$$

同样可以证明齐次对象的缩放因子在分子和分母之间相消. 因此(2.21)的确在射影框架下不变. 所证明的结果概括为：

##### 结论2.22  —旦二次曲线$\mathrm C_\infty^\*$在射彩平面上被辨认，那么欧氏角可以用(2.21)来测置.

作为它的推论有

##### 结论2.23  如果$\mathbf l^\intercal \mathrm C_\infty^\* \mathbf m=\mathbf 0$, 则直线$\mathbf l$和$\mathbf m$正交.

几何上，如果直线$\mathbf l$和$\mathbf m$满足$\mathbf l^\intercal \mathrm C_\infty^\* \mathbf m=\mathbf 0$, 则称两直线相对于二次曲线$\mathrm C_\infty^\*$共轭(见节2.8.1).

#### 长度比

一旦$\mathrm C_\infty^\*$被辨认，长度比同样可以测量. 考察图2.16中顶点为$\bf a, b, c$的三角形. 按三角学的正弦定理，长度比$d(\mathbf b, \mathbf c):d(\mathbf a,\mathbf c)=\sin\alpha : \sin\beta$,其中$d(\mathbf x, \mathbf y)$表示点$\mathbf x, \mathbf y$的欧氏距离. 在$\mathrm C_\infty^\*$已被辨认的射影框架下，按(2.21)，$\cos\alpha, \cos\beta$可由$\mathbf l' = \mathbf a'\times \mathbf b', \mathbf m' =\mathbf c'\times \mathbf a'$和$\mathbf n' =\mathbf b'\times \mathbf c'$计算出来. 因此$\sin\alpha, \sin\beta$从而比率$d(\mathbf a, \mathbf b):d(\mathbf c,\mathbf a)$可由射影映射后的点来确定.

![image-2.16](/assets/blogImg/2.16.png)

<font size=2>**图2.16  长度比**. 一旦$\mathrm C_\infty^\*$被辨认，欧氏长度比$d(\mathbf b, \mathbf c):d(\mathbf a,\mathbf c)$可以由射影失真的图形中测量出来. 详见正文.

</font>

### 2.7.5	由图像恢复度量性质

用完全类似于节2.7.2和图2.12中通过辨认$\mathbf l_\infty$来恢复仿射性质的途径，把虚圆点变换到它们的标准位置, 就可以由平面的图像恢复度量性质. 假定在图像上的虚圆点已被辨认，并且图像已用射影变换H矫正使被影像的虚圆点映回到它们在$\mathbf l_\infty$上的标准位置$((1, ±i, 0)^\intercal)$. 由结论2.20可知，世界平面和被矫正的图像之间的变换是相似变换，因为它是保持虚圆点不变的射影变换.

#### 用$\mathrm C_\infty^\*$进行度量矫正

对偶二次曲线$\mathrm C_\infty^\*$几乎包含了实现度量矫正所需的全部信息. 它能确定射影变换中的仿射和射影成分，而只留下相似变换的失真. 这一点可以由它在射影下的变换得到证明. 如果点变换是$\mathbf x' = \mathrm H\mathbf x$, 其中$\bf x$是欧氏坐标而$\mathbf x'$是射影坐标，$\mathrm C_\infty^\*$按结论2.14(p11)$(\mathrm C^{\*'} = \mathrm H\mathrm C^\* \mathrm H^\intercal)$进行变换. 由$\rm H$的分解链(2.16 - p15)可推出：$$\begin{aligned} \mathrm C_\infty^{\*'}&= (\rm H_PH_AH_S)C_\infty^\*(H_PH_AH_S)^\intercal = (H_PH_A)(H_SC_\infty^\*H_S^\intercal)(H_A^\intercal H_P^\intercal) \\\\ &=\rm (H_PH_A)C_\infty^\*(H_A^\intercal H_P^\intercal) \\\\ &= \begin{bmatrix} \mathrm K\mathrm K^\intercal &  \mathrm K\mathrm K^\intercal \mathbf v \\\\ \mathbf v^\intercal  \mathrm K\mathrm K^\intercal & \mathbf v^\intercal  \mathrm K\mathrm K^\intercal \mathbf v\end{bmatrix} \end{aligned}\tag{2.23}$$ 

显然射影成分($\bf v$)和仿射成分(K)可以直接由$\mathrm C_\infty^\*$的像确定，但( 因为根据结论2.21，$\mathrm C_\infty^\*$在相似变换下不变 )相似成分不能确定. 因而，

##### 结论2.24  在射影平面上，一旦$\mathrm C_\infty^\*$被辨认，那么射影失真可以矫正到相差一个相似变换.

实际上，利用SVD(节A3.3(p408)), 可以直接从图像中已辨认的$\mathrm C_\infty^\*$获得所需的矫正单应变换；先将$\mathrm C_\infty^{\*'}$的SVD写为$$\mathrm C_\infty^{\*'}=\rm U\begin{bmatrix} 1&0&0\\\\ 0&1&0\\\\0&0&0 \end{bmatrix}U^\intercal$$

然后通过査对(2.22), 求得相差一个相似变换的矫正射影变换为$\rm H=U$.

下面两个例子给出$\mathrm C_\infty^{\*}$可能在图像中被辨认，从而能获得度量矫正的典型情形.

##### 例2.25  度量矫正 I

假定一幅图像已经仿射矫正( 如上文例2.18 )，那么为了确定度量矫正，我们需要两个约束以便能够确定虚圆点的两个自由度. 这两个约束可以由世界平面上两个直角的影像来获得,

假设已经仿射矫正的图像中的直线$\bf l'$和$\bf m'$与世界平面上的一对垂直线$\bf l$和$\bf m$对应. 由结论2.23得$\mathbf l'^\intercal \mathrm C_\infty^{\*'} \mathbf m'=\mathbf 0$, 并用(2.22)且让$\bf v = 0$得$$(l_1'\quad l_2' \quad l_3')\begin{bmatrix} \mathrm K\mathrm K^\intercal & \mathbf 0 \\\\ \mathbf 0^\intercal  & 0\end{bmatrix}\begin{pmatrix} m_1'\\\\m_2'\\\\m_3' \end{pmatrix}=0$$

它是关于$2\times2$矩阵$\rm S=KK^\intercal$的线性约束. 矩阵$\rm S=KK^\intercal$是对称矩阵并有三个独立元素，因而有两个自由度(因为全局的尺度因子无关紧要). 该正交条件约减为方程$(l_1', l_2')\mathrm S(m_1', m_2')^\intercal = 0$.并可重写为：$$(l_1'm_1', l_1'm_2'+l_2'm_1', l_2'm_2')\mathbf s=0$$

其中$\mathbf s = (s_{11}, s_{12}, s_{22})^\intercal$是$\rm S$的3维矢量形式. 两个这样的正交直线对就能提供两个约束，并可以联合起来给出以$\bf s$为零矢量的$2\times3$矩阵. 这样，在相差一个尺度因子的情况下获得$\rm S$, 并进一步获得$\rm K$( 利用 Cholesky 分解法：节A3.2.1(p407)). 图2.17给出一个例子，它在已进行过仿射矫正的图2.13上用两组正交直线对进行度量矫正.

另外，度量矫正所需的两个约束可以通过一个圆的影像或两个已知的长度比来得到. 对于圆的情形，其像在仿射矫正过的图像中是椭圆，该椭圆和(已知)$\mathbf l_\infty$的交点直接确定被影像的虚圆点.

在下面的例子中，二次曲线$\mathrm C_\infty^{\*}$可以用另一种方法直接在一幅透视图像中加以确定，而不用首先辨认$\mathbf l_\infty$

![image-2.17](/assets/blogImg/2.17.png)

<font size=2>**图2.17  通过正交直线进行度量矫正 I**. 求对仿射图像进行度量矫正的仿射变换可以从被影像的正交直线中计算出来. (a)在仿射矫正过的图像(图2.13)上，两(非平行)直线对被认定与世界平面上的正交直线相对应 (b)度量矫正图像. 注意在度量矫正过的图像中. 所有在世界中正交的直线是正交的，世界正方形的长宽比为1，而世界圆是圆的.

</font>

##### 例2.26  度量矫正 II

这里我们从平面的原有透视图像( 不像例2.25中用仿射矫正过的图像 )入手. 假定直线$\bf l$和$\bf m$是世界平面上两正交直线的图像；则按结论2.23, $\mathbf l^\intercal \mathrm C_\infty^\* \mathbf m=\mathbf 0$, 然后用与(1.4 - p6)(约束二次曲线使之过一点)类似的方式，提供关于$\mathrm C_\infty^{\*}$元素的一个线性约束，即$$(l_1m_1, (l_1m_2+l_2m_1)/2, l_2m_2, (l_1m_3+l_3m_1)/2, (l_2m_3+l_3m_2)/2, l_3m_3)\mathbf c=0$$

其中$\mathbf c=(a, b, c, d, e, f)^\intercal$是$\mathrm C_\infty^{\*}$的二次曲线矩阵(2.3 - p6)的6维矢量形式. 五个这样的约朿联合起来，便形成一个$5\times6$矩阵，使得$\bf c$和$\mathrm C_\infty^{\*}$作为其零矢量而求得. 它证明$\mathrm C_\infty^{\*}$可以由世界平面上五个正交的直线对的图像线性地加以确定. 图2.18给出以这种直线对约束进行度量矫正的一个例子.

![image-2.18](/assets/blogImg/2.18.png)

<font size=2>**图2.18  通过正交直线进行度量矫正 II**. (a)二次曲线$\mathrm C_\infty^{\*}$用图中显示的五个正交直线对在透视图像平面(建筑物的前墙)上被确定. 二次曲线$\mathrm C_\infty^{\*}$确定虚圖点，并等价于确定了度量矫正图像所需的射影变换. (b)图(a)与图2.4(p10)是同一个透视图像，在那里图像的透视失真是通过确定四个图像点的世界位置来消除的.

</font>

#### 分层法

注息：在例2.26中，仿射和射影失真通过确定$\mathrm C_\infty^{\*}$一次性给予解决. 而前面的例2.25则先消除射影失真，然后消除仿射失真. 这种两步法称为分层法. 类似的方式适用于3D情况，并用于第9章的3D重构和第18章的自|标定，它们由3D射影重构获得度量重构.

## 2.8  二次曲线的其他性质

现在介绍点、线和二次曲线之间的一种被称为**配极**的重要几何关系. 这种（正交性表示的）关系的应用将在第七章中给出.

### 2.8.1  极点 - 极线关系

点$\bf x$和二次曲线C定义一条直线$\mathbf l=\mathrm C\mathbf x$. $\bf l$称为$\bf x$关于C的极线，而点$\bf x$称为$\bf l$关于C的极点.

* **点$\bf x$关于二次曲线C的极线$\mathbf l=\mathrm C\mathbf x$与C交于两点. C的过这两点的两条切线相交于$\bf x$.**

这个关系在图2.19中给予说明.

![image-2.19](/assets/blogImg/2.19.png)

<font size=2>**图2.19  极点一极线关系**. 直线$\mathbf l=\mathrm C\mathbf x$足点$\bf x$关于二次曲线C的极线，而点$\mathbf x=\mathrm C^{-1}\mathbf l$是$\bf l$关于$\rm C$的极点. $\bf x$的极线与二次曲线的交点是$\bf x$到二次曲线的切线的切点. 如果$\bf y$在$\bf l$上，则$\mathbf y^\intercal\mathbf l=\mathbf y^\intercal \mathrm C\mathbf x = 0$. 满足$\mathbf y^\intercal \mathrm C\mathbf x = 0$的点$\bf x$和$\bf y$共轭.

</font>

**证明**  考察C上的一点$\bf y$. C的过$\bf y$的切线是$\mathrm C\mathbf y$. 若满足$\mathbf x^\intercal \mathrm C\mathbf y = 0$, 则$\bf x$就在此切线上. 利用C的对称性，条件$\mathbf x^\intercal \mathrm C\mathbf y=(\mathrm C\mathbf x)^\intercal\mathbf y = 0$表明点$\bf y$在极线$\mathrm C\mathbf x$上. 因此，极线$\mathrm C\mathbf x$与二次曲线交于$\bf y$, $\bf x$在过$\bf y$的切线上.

在$\bf x$趋近二次曲线的过程中，两切线变得越来越接近共线，且它们与二次曲线上的接触点也变得越来越靠近. 在极限位置时, $\bf x$在C上，它的极线与C有二阶接触点$\bf x$，于是我们得到：

* **如果点$\bf x$在C上，则它的极线就是二次曲线过$\bf x$点的切线**.

见结论2.7(p7).

**例2.27**  半径为$r$，中心位于$x$轴上的点$x = a$处的圆的方程为$(x-a)^2+y^2=r^2$, 并用二次曲线矩阵表示为$$\mathrm C=\begin{bmatrix} 1&0&-a\\\\0&1&0\\\\-a&0&a^2-r^2 \end{bmatrix}$$

原点的极线由$\mathbf l = \mathrm C(0, 0, 1)^\intercal =(-a, 0, a^2-r^2)^\intercal$给定. 这是在$x = (a^2-r^2)/a$处的竖直线. 如果$r =a$, 原点在圆周上. 在这种情况下，其极线是$y-$轴并与圆周相切.

显然，二次曲线诱发了$\mathbb P^2$中点与直线之间的一个映射. 这个映射具有射影结构，因为它仅仅涉及相交和相切这两种在射影变换下不变的性质. 点与直线之间的射影变换称为**对射**（不幸的是此名字还有许多别的用处）.

#### 定义2.28  对射

对射是$\mathbb P^2$点到$\mathbb P^2$线的可逆映射. 并用一个$3\times3$非奇异矩阵$\rm A$衣示为$\mathbf l= \mathrm A\mathbf x$.

对射提供了点与直线关系对偶化的一种系统方法. 对射不要求一定要用对称矩阵表示，但因为讨论二次曲线，在这里我们只限于考虑对称的对射.

* **共轭点  如果点$\mathbf y$在极线$\mathbf l=\mathrm C\mathbf x$上，则$\mathbf y^\intercal \mathbf l= \mathbf y^\intercal \mathrm C\mathbf x = 0$. 满足$\mathbf y^\intercal \mathrm C\mathbf x = 0$的任何两点$\bf x$, $\bf y$称为关于二次曲线C共轭**.

共轭关系是对称的：

* **如果$\bf x$在$\bf y$的极线上，那么$\bf y$也在$\bf x$的极线上.**

这一点很简单，因为二次曲线矩阵是对称的——如果$\mathbf x^\intercal \mathrm C\mathbf y = 0$, 则$\bf x$在$\bf y$的极线上，而且如果$\mathbf y^\intercal \mathrm C\mathbf x = 0$, 则$\bf y$在$\bf x$的极线上. 因为$\mathbf x^\intercal \mathrm C\mathbf y = \mathbf y^\intercal \mathrm C\mathbf x$，如果其中一边为零，则另一边也为零. 同时，如果$\mathbf l^\intercal \mathrm C^\* \mathbf m = 0$, 则存在一个关于直线$\bf l$和$\bf m$的对偶共轭关系.

### 2.8.2	二次曲线的分类

本节介绍二次曲线的射影和仿射分类.

#### 二次曲线的射影标准形式

因为C是对称矩阵，所以有实特征值并可分解为乘积$\rm C=U^\intercal DU$ (见节A3.2(p406))，其中U是正交矩阵，而D是对角矩阵. 以射影变换U作用于二次曲线C,则C被变换成另一条二次曲线$\rm C' = U^{-\intercal}CU^{-1} = U^{-\intercal}U^\intercal DUU^{-1}=D$. 这表明任何二次曲线都射影等价于一个由对角矩阵表示的二次曲线. 令$\mathrm D = \mathrm d\mathrm i\mathrm a\mathrm g(\varepsilon_1d_1, \varepsilon_2d_2, \varepsilon_3d_3)$其中$\varepsilon_i=±1$或0且$d_i>0$.则D可以写为，$$\mathrm D = \mathrm d\mathrm i\mathrm a\mathrm g(s_1, s_2, s_3)^\intercal \mathrm d\mathrm i\mathrm a\mathrm g(\varepsilon_1, \varepsilon_2, \varepsilon_3)\mathrm d\mathrm i\mathrm a\mathrm g(s_1, s_2, s_3)$$

其中$s_i^2=d_i$. 注意$\mathrm d\mathrm i\mathrm a\mathrm g(s_1, s_2, s_3)^\intercal= \mathrm d\mathrm i\mathrm a\mathrm g(s_1, s_2, s_3)$. 现在用变换$\mathrm d\mathrm i\mathrm a\mathrm g(s_1, s_2, s_3)$再进行一次变换，二次曲线D被变为具有矩阵$\mathrm d\mathrm i\mathrm a\mathrm g(\varepsilon_1, \varepsilon_2, \varepsilon_3)$的二次曲线，其中$\varepsilon_i=\pm1$或0.

| 对角线   | 方程            | 二次曲线类型                  |
| :--------: | :---------------: | :-----------------------------:|
| (1.1.1)  | $x^2+y^2+w^2=0$ | 假二次曲线——无实点            |
| (1.1.-1) | $x^2+y^2-w^2=0$ | 圆                            |
| (1.1.0)  | $x^2+y^2=0$     | 单个实点$(0, 0, 1)^\intercal$ |
| (1.-1.0) | $x^2-y^2=0$     | 两条直线$x=\pm y$             |
| (1,0.0)  | $x^2 = 0$       | 单条直线$x = 0$计两次         |

<font size=2>**表2.2  点二次曲线的射影分类**.  任何平面二次曲线都射影等价于表中给出的一种类型. 对某$i$，有$\varepsilon_i=0$的那些二次曲线是退化的二次曲线，它们可以用秩小于3的矩阵表示. 本表的二次曲线类型栏仅介绍二次曲线的实点——例如记为复二次曲线时$x^2 + y^2 =0$由一对直线$x= ±iy$组成.

</font>

可以用置换矩阵进一步变换，以保证值$\varepsilon_i = 1$出现在值$\varepsilon_i = -1$之前而后者又在值$\varepsilon_i=0$之前.最后如果有必要，可以乘以-1以保证+1至少和-1一样多. 现在可以列举各种类型的二次曲线，并在表2.2中给出.

#### 二次曲线的仿射分类

众所周知，在欧氏几何中，(非退化或真)二次曲线可以分为双曲线、椭圆和抛物线. 如上文所示，在射影几何中这三种类型的二次曲线却都射影等价于圆. 然而在仿射几何中，上述欧氏分类仍有效，因为它仅取决于$\mathbf l_\infty$与二次曲线的关系. 三种类型的二次曲线与$\mathbf l_\infty$的关系在图2.20中给出了说明.

![image-2.20](/assets/blogImg/2.20.png)

<font size=2>**图2.20  点二次曲线的仿射分类**.  二次曲线是(a)椭圆，(b)抛物线，(c)双曲线；分别取决于它们与$\mathbf l_\infty$的关系：(a)无实交点、(b)相切(2点接触)、(c)有2个实交点. 在仿射变换下，$\mathbf l_\infty$是不动线而且交点保持不变. 因此，这种分类在仿射变换下不变.

</font>

## 2.9  不动点与直线

由$\mathbf l_\infty$和虚圆点的例子，我们已经知道点和直线在射影变换下可能是不动的. 本节将对该思想作更彻底地研究.

这里，我们把源平面和目标平面视为等同(一样)，这样可把点$\bf x$映射到点$\bf x'$的变换在同一坐标系中进行. 关键思想是变换的一个**特征矢量**对应一个**不动点**，因为对于特征值$\lambda$及其对应的特征矢量$\bf e$有$$\mathrm H\mathbf e=\lambda\mathbf e$$

而$\bf e$和$\lambda \mathbf e$表示同一点. 通常在计算机视觉应用中，特征矢量和特征值具有物理的或几何的重要意义.

一个$3\times3$矩阵有三个特征值，如果特征值互不相同，则一个平面射影变换最多有三个不动点. 因为在此情形中特征方程是三次方程，特征值及其对应的特征矢量中有一个或三个是实的. 类似的推导可以用于不动直线，它对应于$\mathrm H^\intercal$的特征矢量，因为直线的变换(2.6 - p10)为$\mathbf l' = \mathrm H^{-\intercal} \mathbf l$.

不动点和不动直线之间的关系在图2.21中显示. 注意直线的不动是集合不动，不是点点不动，即该直线上的一点被映射到该直线上的另一点，这两点一般不相同. 这并不难理解：平面射影变换诱导直线上的一个1D射影变换. 1D射影变换以一个$2\times2$的齐次矩阵表示(节1.5). 对应于该$2\times2$矩阵的两个特征矢最，1D射影变换有两个不动点. 这些不动点也是2D射影变换的不动点.

![image-2.21](/assets/blogImg/2.21.png)

<font size=2>**图2.21  平面射影变换的不动点和直线.** 这里有三个不动点和过这三点的不动直线. 不动直线和不动点可能是复的. 从代数的角度来说，不动点是点变换($\mathbf x' = \mathrm H\mathbf x$)的特征矢量$\mathbf e_i$, 而不动直线是线变换$(\mathbf l' = \mathrm H^{-\intercal} \mathbf l)$的特征矢量. 注意，不动直线不是点点不动：在变换下，直线上的一点被映为其上的另一点，只有不动点才映为自身.

</font>

进一步的特殊性涉及重特征值的情况. 假定两个特征值(如$\lambda_2, \lambda_3$)相等，而对应于$\lambda_2=\lambda_3$存在两个不同的特征矢量$(\mathbf e_2, \mathbf e_3)$,那么包含特征矢量$\mathbf e_2、\mathbf e_3$的直线将是点点不动的, 即它是由不动点构成的直线. 假定$\mathbf x = \alpha\mathbf e_2+\beta \mathbf e_3$;则有$$\mathrm H\mathbf x=\lambda_2\alpha\mathbf e_2+\lambda_2\beta\mathbf e_3=\lambda _2\mathbf x$$

即过两退化特征矢量的直线上的点都映射为自己(仅仅相差一比例因子). 另一种可能是$\lambda_2=\lambda_3$，但只有一个对应的特征矢量. 在这种情况下，特征矢量的**代数维数**为二，而**几何维数**为一. 其不动点少了一个(2个而不是3个). 重特征值的各种情况将在附录5(p427)和p33的练习(8)中作进一步讨论.

我们现在来査看一下节2.4介绍的射影变换子群中的不动点和直线. 仿射变换以及比它更特殊的变换有两个特征矢量, 它们都是理想点$(x_3=0)$并且对应于左上角$2\times2$矩阵的特征矢量. 第三个特征矢量通常是有限矢量.

#### 欧氏矩阵

两个不动理想点是虚圆点$\bf I,J$组成的复共轭对，相对应的特征值是$\{ e^{i\theta}, e^{-i\theta} \}$, 这里$\theta$是旋转角. 对应于特征值1的第三个特征矢量，称为**极点**. 欧氏变换等价于绕该点转$\theta$角的纯旋转并且没有平移.

一种特殊的情况是纯平移(即$\theta$ = 0). 这时特征值三重退化. 无穷远线点点不动，且有一束过点$(t_x, t_y, 0)^\intercal$的不动直线，该点对应于平移方向. 因此平行于$\bf t$的直线是不动的. 这是约束透视变换的一个例子(见节A5.3(p430)).

#### 相似矩阵

两个不动理想点仍是虚圆点. 特征值是$\{1, se^{i\theta}, se^{-i\theta}\}$. 相似变换的作用可以理解为绕它的有限不动点的旋转和取$s$为因子的均匀缩放. 注意虚圆点的特征值仍然表征旋转角.

#### 仿射矩阵

两个不动理想点可以是实或复共轭的，但在任何一种情况下，过这些点的不动直线$\mathbf l_\infty=(0, 0, 1)^\intercal$是实的.

## 2.10  结束语

### 2.10.1	文献

关于平面射影几何的一些浅显的介绍在Mundy和Zisserman[Mundy - 92]的附录中给出，它是为计算机视觉研究者而写的. 更正规的介绍在Semple和Kneebone[Semple-79]中给出，但[Springer - 64]更容易读懂.

关于由平面的图像恢复仿射和度量景物性质的工作有：Collins和Beveridge[Collins - 93]用消影线由卫星图像恢复仿射性质，而Liebowitz和Zisserman[Liebowitz - 98]利用平面的如直角一类的度量信息来恢复度量几何.

### 2.10.2	注释与练习
#### 1. 仿射变换
   (a) 证明仿射变换能把圆映射为椭圆，但不能把捕圆映射为双曲线或抛物线.

   (b) 证明在仿射变换下平行的两线段的长度比不变，而不平行的线段的长度比则 不是如此.
#### 2. 射影变换
   证明存在使过原点的单位圆不动(作为集合不动)的一种三参数簇的射影变换，即以原点为中心的单位圆被映射到以原点为中心的单位圆(提示，用结论2.13(p11)计算变换). 该簇的几何解释是什么？
#### 3. 各向同性
   证明两直线在相似变换下具有一个不变量: 两直线和两点在射影变换下具有一个不变量. 在这两种情况下，自由度的计算法则(结论2.16(p17))的等式情况不成立. 证明在这两种情况中，相应的变换不能完全被确定，虽然它能够部分被确定.
#### 4. 不变量
   用点、直线和二次曲线的变换规则证明：

   (a) 两条直线$\bf l_1, \bf l_2$及不在其上的两点$\mathbf x_1, \mathbf x_2$有不变量$$I=\frac{(\mathbf l_1^\intercal \mathbf x_1)(\mathbf l_2^\intercal \mathbf x_2)}{(\mathbf l_1^\intercal \mathbf x_2)(\mathbf l_2^\intercal \mathbf x_1)}$$（见前一问题）

   (b) 对于二次曲线C和在一般位置上的两点$\bf x_1, x_2$有不变量：$$I=\frac{(\mathbf x_1^\intercal \mathrm C\mathbf x_2)^2}{(\mathbf x_1^\intercal \mathrm C\mathbf x_1)(\mathbf x_2^\intercal \mathrm C\mathbf x_2)}$$

   (c) 证明计算夹角的射影不变量表达式(2.21)等价于Laguerre关于虚圆点交比的射影不变量表达式(见[Springer-64]).
#### 5. 交比
   证明在直线的射影变换下，四共线点的交比不变(2.17 - p17). 提示：首先把直线上两点的变换写成$\overline{\mathbf x}\_i'= \lambda\_i\mathrm H\_{2\times2}\overline{\mathbf x}\_i$和$\overline{\mathbf x}\_j'= \lambda\_j\mathrm H_{2\times2}\overline{\mathbf x}\_j$, 这里的等式不相差一个比例因子，然后由行列式性质证明$\left| \overline{\mathbf x}\_i'\overline{\mathbf x}\_j' \right| =\lambda\_1\lambda\_2\mathrm d\mathrm e\mathrm t \\;\mathrm H_{2\times2}\left| \overline{\mathbf x}\_i\overline{\mathbf x}\_j \right|$，并由此继续. 另一种推导方法在[Semple-79]中给出.
#### 6. 配极
   图2.19给出了椭圆**外**一点$\bf x$的极线的几何作图. 给出当点在椭圆内时极线的几何作图. 提示：选择过$\bf x$的任意直线. 该直线的极点是$\bf x$极线上的一点.
#### 7. 对偶二次曲线
   证明矩阵[$\bf l$]$_\times\mathrm C$[$\mathbf l$]$_\times$表示一个秩为2的对偶二次曲线，它由直线$\mathbf l$与(点)二次曲线C的两个交点组成.（记号[$\bf l]$$_\times$在(A3.4-p410)中定义）.
#### 8. 不动点
   给出只有一个和两个不动点的2D单应变换的例子. 它们对应于几何维数小于代数维数的重特征值.
#### 9. 特殊射影变换
   假定一个景物平面上的点由一条直线的反射相关联，例如具有双边对称的平面物体. 证明该平面透视图像的点由满足$\rm H^2 = I$的射影变换H相关联. 进一步证明在H下有一条不动点组成的直线，它对应于反射直线的影像，并且H有一个不在此直线上的特征矢量，它是该反射方向的消影点（H是一个平面调和透射，见节 A5.2(p427)）.

   现在，假定点由有限对称旋转相联系：例如在一个六边形螺栓头上的点. 证明在这种情况下$\rm H^n=I$，这里$n$是该对称旋转的阶(六边形为6)，H的特征值确定旋转角，而对应于实特征值的特征矢量是对称旋转中心的影像.