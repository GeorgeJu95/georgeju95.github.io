---
title: 多视角几何总结
date: 2019-08-02 20:40:36
categories: 
tags: [多视角几何]
---

# 第一章 多视角几何纵览

* 平面的射影变换定义为平面上点的任意**保直线映射**。

* **射影空间**是欧几里德空间的延伸(通过齐次向量表示点)，两条线总是在一个点上相遇(有时这个点是无穷远点)。

* 二维射影空间中的无穷远点形成一条线，通常称为**无穷远线**。它们在三维空间中形成了**无穷远平面**。
<!-- more -->
* 空间自身变换和旋转到了不同位置——**欧式变换**；空间的移动、旋转，以及最后在不同方向可能按不同比例进行线性缩放——**仿射变换**。欧氏变换或仿射变换的结果是，无穷远点仍然在无穷远。

* 射影空间$\mathbb P^n$的**射影变换**表示为齐次坐标的线性变换。$$ \mathbf X^\prime=\mathrm H_{(n+1)\times (n+1)}\mathbf X. $$ 无穷远点映射到任意其它点，没有保持无穷远点。

* 射影平面的几何加上一条特殊的线被称为**仿射几何**，将一个空间中的该特殊线映射到另一个空间中特殊线的任何射影变换称为**仿射变换**。

* 平面的**虚圆环点**：圆方程的齐次坐标$(x,y,w)$形式形如：$$(x-aw)^2+(y-bw)^2=r^2w^2$$该方法用圆心表示圆，圆心用齐次坐标表示为$(x_0,y_0,w_0)^\top=(a,b,1)^\top$。可以很快验证点$(x,y,w)^\top=(1,\pm i,0)^\top$在这类圆上。重复一下这个有趣的事实，<u>每个圆都通过点</u>$(1,\pm i,0)^\top$，<u>因此它们存在于任意两个圆的交点</u>。由于它们最后的坐标为零，因此这两个点位于无穷远线上。由于显而易见的原因，它们被称为<mark>平面的虚圆环点（circular point）</mark>。

* 位于无穷远平面上的二阶曲线（圆锥曲线），且仅由复数点构成。称为**绝对圆锥曲线**：通过确定无穷远线和一对虚圆环点，我们清楚了如何根据射影平面确定欧氏平面。同样的思想可用于三维几何。如同二维情况，可以仔细观察球体，考察它们如何相交。就像代数上认为的那样，也如同两个一般椭球（或其它的二次曲面）那样，两个球体交于一个圆，而非一般的四阶曲线。这一思路导致了新发现，在齐次坐标系$(\mathrm X,\mathrm Y,\mathrm Z,\mathrm T)^\top$，所有球体与无穷远平面相交的曲线满足方程：$\mathrm X^2+\mathrm Y^2+\mathrm Z^2=0; \mathrm T=0$。这是位于无穷远平面上的二阶曲线（圆锥曲线），且仅由复数点构成。它称为<mark>绝对圆锥曲线</mark>。尤其鉴于它和摄像机标定相关，它是本书的关键几何实体之一，正如将在后文看到的那样。以上方程定义的绝对圆锥曲线，只存在于欧氏坐标系统。挑选一个特殊的平面作为无穷远平面，并指定位于该平面的一条特殊的圆锥曲线作为绝对圆锥曲线，通常我们可以认为三维欧氏空间是从射影空间由此推导的。基于射影空间的坐标系统，可以对这些实体进行很普适的描述。

* 通过确定无穷远线和一对虚圆环点，即根据**射影平面确定欧氏平面**。挑选一个特殊的平面作为无穷远平面，并指定位于该平面的一条特殊的圆锥曲线作为绝对圆锥曲线，即**射影空间确定三维欧式空间**。

* **线的垂直**可以用绝对圆锥曲线来定义。空间中直线的垂直不是仿射几何中的合法概念，但是属于欧氏几何</mark>。线的垂直可以用绝对圆锥曲线来定义，如下所述。延长两条线，直到它们与平面交于无穷远，我们得到的两点称为这两条线的方向点（direction）。线的垂直通过到绝对圆锥曲线的这两个方向点间的关系确定。<mark>如果两个方向点是关于绝对圆锥曲线的共轭点，则直线是垂直的</mark>（参见图3.8（p83）[图2.8 v1-p48]）。共轭点的几何和代数表示在2.8.1节（p58）[v1 p29]中定义。简而言之，如果绝对圆锥曲线由3×3对称矩阵$\mathrm\Omega_\infty$表示，并且方向点是$\mathbf d_1$和$\mathbf d_2$，当$\mathbf d_1^\top\mathrm\Omega_\infty\mathbf d_2=0$时，那么它们关于$\mathrm\Omega_\infty$共轭。

* **摄像机矩阵**：为了在成像过程应用射影几何，通常将现实世界建模为三维射影空间，等价于$\mathbb R^3$附带无穷远点。图像模型是类似的二维射影平面$\mathbb P^2$。中心射影不过是$\mathbb P^3$到$\mathbb P^2$的一个映射。如果我们考虑点记为齐次坐标$(\mathrm X,\mathrm Y,\mathrm Z,\mathrm T)^\top$的形式，且射影中心是原点$(0,0,0,1)^\top$，那么我们看到，固定$\mathrm X$、$\mathrm Y$和$\mathrm Z$并改变$\rm T$所得的全部点$(\mathrm X,\mathrm Y,\mathrm Z,\mathrm T)^\top$组成的集合，形成穿过射影中心点的一条射线，因而这些点都映射到相同的点。因此，$(\mathrm X,\mathrm Y,\mathrm Z,\mathrm T)$的最后坐标和点在哪里成像无关。像点（image point）事实上是$\mathbb P^2$中的点，齐次坐标为$(\mathrm X,\mathrm Y,\mathrm Z)$。因此，该映射可表示为三维齐次坐标的一个映射，记为$3\times 4$的矩阵$\mathrm P$，其块结构记为$\mathrm P=\left[\mathrm I_{3\times 3}\vert\mathbf 0_3\right]$，其中$\mathrm I_{3\times 3}$是$3\times 3$的单位矩阵，$\mathbf 0_3$是三维零向量。考察不同的射影中心，以及图像不同的射影坐标系，最一般的成像射影表示为任一秩3的$3\times 4$矩阵，它作用于$\mathbb P^3$中点的齐次坐标，将其映射到$\mathbb P^2$中的成像点。该矩阵$\mathrm P$称为<mark>摄像机矩阵</mark>。
总之，射影摄像机在空间中一个点上的操作，可利用线性的齐次坐标映射表示为：$$\begin{pmatrix}x \\\\ y \\\\ w\end{pmatrix}=\mathrm{P}\_{3\times4}\begin{pmatrix}\mathrm{X} \\\\ \mathrm{Y} \\\\ \mathrm{Z} \\\\ \mathrm{T}\end{pmatrix}$$此外，如果所有点都位于一个平面上（我们可以将其选择为平面Z = 0），那么线性映射将简化为$$\begin{pmatrix}x \\\\ y \\\\ w\end{pmatrix}=\mathrm{H}_{3\times3}\begin{pmatrix}\mathrm{X} \\\\ \mathrm{Y} \\\\ \mathrm{T}\end{pmatrix}$$它是一种射影变换。

* 为了彻底弄清图像与现实世界的欧氏关系，有必要叙述它们相关的欧氏几何。如我们所见，通过设定$\mathbb P^3$中特定平面为无穷远平面，确定了三维世界的欧氏几何，并将该平面中特定的圆锥曲线$\mathrm\Omega$作为绝对圆锥曲线。对位于无穷远平面之外的摄像机，现实世界中的无穷远平面一一映射到了像平面。这就是为什么图像中任一点确定了空间中的一条射线，该射线与无穷远平面交于一点。因此，现实世界中的无穷远平面，不会告诉我们关于图像的任何新东西。绝对圆锥曲线，尽管是无穷远平面中的一条圆锥曲线，必投影为图像中的一条圆锥曲线。所得的图像曲线，称为<mark>绝对圆锥曲线的图像（Image of the Absolute Conic），或者IAC</mark>。若已知IAC在图像中的位置，那么我们称摄像机<mark>已标定</mark>。

* 从双视中重建点集的基本工具是<mark>基础矩阵</mark>（fundamental matrix），它描述了同一三维点的像点$\mathbf x$和$\mathbf x^\prime$满足的约束。该约束源自于这两个视图的摄像机中心、像点以及空间点的共面性。令基础矩阵为$\mathrm F$，匹配的点对$\mathbf x_i\leftrightarrow \mathbf x_i^\prime$必须满足: $$\mathbf x_i^{\prime\top}\mathrm F\mathbf x_i = 0$$其中$\mathrm F$是一个秩为$2$的$3\times 3$矩阵。这些方程是线性的，系数是矩阵$\mathrm F$。这意味着若$\mathrm F$未知，那么它可由一组对应点计算。一对摄像机矩阵$\mathrm P$和$\mathrm P^\prime$唯一确定了一个基础矩阵$\mathrm F$，反之，该基础矩阵确定了三维射影歧义下一对摄像机矩阵。因此，基础矩阵蕴涵了这对摄像机的全部射影几何，并且不被三维射影变换所改变。
基础矩阵法重建场景非常简便，包含以下步骤：
  * 给定两视图间的几个对应点$\mathbf x_i\leftrightarrow\mathbf x_i^\prime$，基于共面方程$\mathbf x_i^{\prime\top}\mathrm F\mathbf x_i=0$，构造系数为$\mathrm F$的线性方程。
  * 找出线性方程组的解$\mathrm F$。
  * 根据9.5节(p253[v1 p171])中给出的简单公式由$\mathrm F$计算一对摄像机矩阵。
  * 给定两个摄像机$\left(\mathrm P, \mathrm P^\prime\right)$及对应像点对$\mathbf x_i\leftrightarrow\mathbf x_i^\prime$，找出投影到给定像点的三维点$\mathbf X_i$。这种求解$\mathbf X$的方法称为<mark>三角测量法</mark>。

此处只给出了算法的梗概，本书详细探讨了其中的每部分。


# 第二章 $\rm 2D$射影几何和变换

* 矢量就是点，对称矩阵就是二次曲线(一段圆锥曲线)

* 齐次矢量：对于线，形如$ax+by+c=0$的方程表示平面上的一条直线，因此也可用矢量$(a, b, c)^\intercal$表示；对于点，把“1”作为增加的最后一个坐标使$\mathbb R^2$中的点$(x, y)^\intercal$表示成3维矢量。

* **结论2.1**：点$\mathbf x$在直线$\mathbf l$上的充要条件是$\mathbf x^\intercal \mathbf l = 0$.

* **结论2.2**：两直线$\mathbf l$和$\mathbf l'$的交点是点$\mathbf x=\mathbf l \times \mathbf l'$。注意：两直线交点的表示之所以这样简洁是因为采用了直线和点的齐次坐标表示。

* **结论2.4**：过两点$\mathbf x$和$\mathbf x'$的直线是$\mathbf l = \mathbf x \times \mathbf x'$.

* 所有理想点的集合可以写成$(x1, x2, 0)^\intercal$，并由比率$x1:x2$指定一个具体的点。注意该集合在一条直线上，称为**无穷远线**，用矢量$\mathbf l_\infty = (0, 0, 1)^\intercal$表示。我们可以验证$(0, 0, 1)(x1, x2, 0)^\intercal=0$.

* **射影平面的模型**：一种有益的方法是把$\mathbb P^2$看作$\mathbb R^3$中一种射线的集合。该集合的所有矢量$k(x1, x2, x3)^\intercal$当k变化时形成过原点的射线。这样的一条射线可以看作是$\mathbb P^2$中的一个点。在此模型中，$\mathbb P^2$中的直线是过原点的平面。可以验证两相异的射线共处于一张平面上，而任何两张相异平面相交于一条射线。这类似于两个相异的点唯一确定一条直线，而两条相异的直线总相交于一点。

  点和线可以用平面$x3=1$与这些射线和平面集相交得到。如图2.1所示，表示理想点的射线和表示$\mathbf l_\infty$的平面都与平面$x3=1$平行.

  ![image-2.1](/assets/blogImg/2.1.png)

  <font size=2>**图2.1 射影平面的模型.** $\mathbb P^2$的点和线分别表示为$\mathbb R^3$中过原点的射线和平面.$x1x2-$平面上的射线表示理想点，而$x1x2-$平面表示$\mathbf l_\infty$

  </font>
  
* 对偶原理：2维射影几何中的任何定理都有一个对应的对偶定理，它可以通过互换原定理中点和线的作用而导出。

* **二次曲线**：二次曲线由平面上的二阶方程描述. 在欧氏几何中，二次曲线有三种主要类型: 双曲线，椭圆和抛物线(后面将定义的所谓的退化二次曲线除外)，在经典理论中，这三类曲线是不同方向的平面与圆锥相交所产生的截线(退化的二次曲线由过锥顶的平面产生). 但是我们将了解到，在2D射影几何中所有非退化的二次曲线在射影变换下都等价.

  在非齐次坐标中，二次曲线的方程是$$ax^2+bxy+cy^2+dx+ey+f=0$$对应于一个二阶多项式. 通过替代$x \mapsto x_1/x_3, y \mapsto x_2/x_3$，"齐次化"得到$$\begin{equation} ax_1^2+bx_1x_2+cx_2^2+dx_1x_3+ex_2x_3+fx_3^2=0 \tag{2.1} \end{equation}$$

  或表示成矩阵形式$$\mathbf x^\intercal \mathbf C \mathbf x = 0 \tag{2.2}$$

  其中二次曲线系数矩阵$\mathbf C$为$$\mathbf C = \begin{bmatrix} a & b/2 & d/2 \\\\ b/2 & c & e/2 \\\\ d/2 & e/2 & f \end{bmatrix} \tag{2.3}$$

  注意二次曲线的系数矩阵是对称的. 它与点和线的齐次表达式一样，重要的仅仅是矩阵元素的比率，因为用一个非零标盘乘$\mathbf C$不会影响上面的方程. 因此，$\mathbf C$是一条二次曲线的齐次表示. 二次曲线有五个自由度，可以视为比率$\left| a : b : c : d : e : f \right|$或等价地视为对称矩阵六个元素减去一个比例因子.

* **结论4**：过(非退化)二次曲线$\mathrm C$上点$\mathbf x$的切线$\mathbf l$由$\mathbf l = \mathrm C \mathbf x$确定.

  **证明**  因为$\mathbf l^\intercal \mathbf x = \mathbf x^\intercal \mathrm C \mathbf x = 0$, 所以直线$\mathbf l = \mathrm C \mathbf x$过$\mathbf x$. 如果$\mathbf l$仅与二次曲线交于一点，那么它就是切线，我们的证明也就完成了. 否则，假定$\mathbf l$与该二次曲线还交于另一点$\mathbf y$，则有$\mathbf y^\intercal \mathrm C \mathbf y = 0$和$\mathbf x^\intercal \mathrm C \mathbf y =\mathbf l^\intercal \mathbf y = 0$. 由此推出$(x+\alpha y)^\intercal \mathrm C (x+\alpha y)=0$对所有$\alpha$成立，这表明连接$\mathbf x$和$\mathbf y$的整条直线$\mathbf l = \mathrm C \mathbf x$在该二次曲线$\mathrm C$上，因而C是退化的（见下文）.

* **对偶(线)二次曲线**：上面定义的二次曲线$\mathrm C$更确切地应称为**点**二次曲线，因为它定义的是点的方程。 给出了$\mathbb P^2$的对偶结论以后，理所当然应该有一个由直线的方程定义的二次曲线. 这种**对偶**（或线）二次曲线也由一个$3 \times 3$矩阵表示，我们把它记为$\mathrm C^\*$. 二次曲线$\mathrm C$的切线$\mathbf l$满$\mathbf l^\intercal \mathrm C^\* \mathbf l = 0$. 其中$\mathrm C^\*$表示$\mathrm C$的伴随矩阵（伴随矩阵在附录4(p578[p404])的节A4.2(p580[406])中定义）. 对非奇异对称矩阵$\mathrm C$有$\mathrm C^\* = \mathrm C^{-1}$(相差一个尺度因子).

  对偶二次曲线也称**二次曲线的包络**，其理由在图2.2中给予说明. 对偶二次曲线有五个自由度. 与点二次曲线相类似，一般位置上的五条线定义一条对偶二次曲线.

  ![image-2.2](/assets/blogImg/2.2.png)

  <font size=2>**图2.2**	(a)满足$\mathbf x^\intercal \mathrm C \mathbf x = 0$的点$\mathbf x$在一条点二次曲线上.（b)满足$\mathbf l^\intercal \mathrm C^\* \mathbf l = 0$的直线$\mathbf l$是点二次曲线$\mathrm C$的切线，即二次曲线$\mathrm C$是直线$\mathbf l$的包络.

  </font>

* **退化二次曲线**：非满秩矩阵$\mathrm C$所定义的二次曲线称作退化二次曲线. 退化的点二次曲线包含两条线（秩2）或一条重线（秩1).

* **射影映射 = 保线变换 = 射影变换 = 单应**

* **定理 2.10**：映射$h$ : $\mathbb P^2 \rightarrow \mathbb P^2$是**射影映射**的充要条件是：存在一个$3\times3$非奇异矩阵$\mathrm H$, 使得$\mathbb P^2$的任何一个用矢量$\mathbf x$表示的点都满足$h(x)=\mathrm H \mathbf x$.

  **证明**  令$x1, x2$和$x3$同在一条直线$\mathbf l$上. 因此，$\mathbf l^\intercal \mathbf x_i = 0, i=1,…,3$. 令$\mathrm H$为非奇异$3\times3$矩阵. 可以证明$\mathbf l^\intercal \mathrm H^{-1} \mathrm H \mathbf x_i = 0$. 因此点$\mathrm H \mathbf x_i$都在直线$\mathrm H^{-\intercal} \mathbf l$上，因而该变换保持共线性.

  其逆命题是每个射影映射都以这种方式出现，但相当难证明.

* **中心投影**是射影变换并可用齐次坐标的线性变换$\mathbf x' = \mathrm H \mathbf x$表示。实际上，如果在两平面上建立的都是欧氏（直角）坐标系，那么这样一种由中心投影定义的映射比一般射影变换有更多的约束. 我们称它为**透视映射**而不是完全的射影映射，它可由一个六自由度的变换来表示. 我们将在节A5.4(p430 v1)中再回过来讨论透视映射.

* 如果点$\mathbf x_i$在直线$\mathbf l$上，那么经射影变换后的点$\mathbf x'_i=\mathrm H \mathbf x_i$在直线$\mathbf l'=\mathrm H^{-\intercal} \mathbf l$上. 即在点变换$\mathbf x'_i=\mathrm H \mathbf x_i$下，直线$\mathbf l$变换为$$\mathbf l'=\mathrm H^{-\intercal} \mathbf l \tag{2.6}$$

  我们也可以换一种写法$\mathbf l'^\intercal=\mathbf l^\intercal \mathrm H^{-1}$. 注意直线和点变换的基本区别. 点变换依据$\mathrm H$, 而直线 (视为行矢量）变换则依据$\mathrm H^{-1}$. 这可以用术语“协变”或“逆变”做解释. 我们称点变换为**逆变**而线变换为**协变**. 这种区别将在第15章讨论张量时重新提到并在附录1(p400 v1)中全面地给予解释.

* **结论2.13**  在点变换$\mathbf x' = \mathrm H \mathbf x$下，二次曲线$\mathrm C$变换为$\mathrm C' = \mathrm H^{-\intercal} \mathrm C \mathrm H^{-1}$.

* **结论2.14**  在点变换$\mathbf x' = \mathrm H \mathbf x$下，对偶二次曲线$\mathrm C'$变换为$\mathrm C^{\*'} = \mathrm H \mathrm C^\* \mathrm H^{\intercal}$.

* **等距(isometric)变换**是平面$\mathbb R^2$的变换，它保持欧氏距离不变(*iso* = 一样，*metric* = 度量). —个等距变换可表示为$$\begin{pmatrix} x' \\\\ y' \\\\ 1 \end{pmatrix} = \begin{bmatrix} \varepsilon \cos\theta &-\sin\theta&t_x \\\\ \varepsilon \sin\theta &\cos\theta&t_y \\\\ 0&0&1 \end{bmatrix}\begin{pmatrix} x \\\\ y \\\\ 1 \end{pmatrix}$$

  其中$\varepsilon = ±1$. 如果$\varepsilon$ = 1那么该等距变换是**保向**的并且也是**欧氏**变换（平移和旋转的复合）. 如果$\varepsilon=-1$, 那么该等距变换是逆向的. 例如反射（由对角矩阵出diag(-1, 1, 1)表示）与欧氏变换的复合.

  欧氏变换是刚体运动的模型. 到目前为止，它们是实用中最重要的等距变换，我们将集中研究它们. 但是，保向等距变换在结构恢复时常会出现多义性.

  平面欧氏变换可以用更简洁的分块形式写为$$\mathbf x'=\mathrm H_\mathrm E \mathbf x=\begin{bmatrix} \mathrm R & \mathbf t \\\\ \mathbf 0^\intercal & 1 \end{bmatrix} \mathbf x \tag{2.7}$$

  其中$\mathrm R$是$2\times2$旋转矩阵（满足$\mathrm R^\intercal \mathrm R = \mathrm R \mathrm R^\intercal=\mathrm I$的正交矩阵），$\mathbf t$是2维平移矢量, 而$\mathbf 0$是2维零矢量. 特殊的情况是纯旋转(当$\mathbf t = 0$)和纯平移(当$\mathrm R=\mathrm I$). 欧氏变换也称为**位移**.

  平面欧氏变换有三个自由度：旋转占一个，平移占两个. 不变量, 例如，长度（两点的距离），角度（两线的夹角）和面积. 如果等距变换左上角的$2\times2$矩阵的行列式为1，它是保向的. **保向**的等距变换形成一个群， 但**逆向**的不是. 这种区别对于下面的相似和仿射变换同样如此.

* **相似变换**是一个等距变换与一个均匀缩放的复合. 当欧氏变换（即没有反射）与均匀缩放复合时，相似变换的矩阵表示为：$$\begin{pmatrix} x' \\\\ y' \\\\ 1 \end{pmatrix} = \begin{bmatrix} s \cos\theta &-s\sin\theta&t_x \\\\ s \sin\theta &s\cos\theta&t_y \\\\ 0&0&1 \end{bmatrix}\begin{pmatrix} x \\\\ y \\\\ 1 \end{pmatrix} \tag{2.8}$$可以用更简洁的分块形式写成$$\mathbf x'=\mathrm H_\mathrm S \mathbf x=\begin{bmatrix} s\mathrm R & \mathbf t \\\\ \mathbf 0^\intercal & 1 \end{bmatrix} \mathbf x \tag{2.9}$$

  其中标量s表示均匀缩放. 相似变换也称**等形**变换，因为它保持了“形状”(形式). 一个平面

  相似变换有四个自由度，比欧氏变换多一个缩放自由度. 相似变换可由两组点对应算出. 两点间的长度不是相似不变量, 但两长度的**比率**是不变量，因其缩放因子相互抵消. 同样地, 面积的比率是不变量，也因为缩放因子(的平方)被抵消. **度量结构**就是确定到只相差一个相似变换的结构.

* **仿射变换**是一个非奇异线性变换与一个平移变换的复合. 它的矩阵表示为$$\begin{pmatrix} x' \\\\ y' \\\\ 1 \end{pmatrix} = \begin{bmatrix} a_{11} &a_{12}&t_x \\\\ a_{21} &a_{22}&t_y \\\\ 0&0&1 \end{bmatrix}\begin{pmatrix} x \\\\ y \\\\ 1 \end{pmatrix} \tag{2.10}$$或分块形式$$\mathbf x'=\mathrm H_\mathrm A \mathbf x=\begin{bmatrix} \mathrm A & \mathbf t \\\\ \mathbf 0^\intercal & 1 \end{bmatrix} \mathbf x \tag{2.11}$$
  
  其中$\mathrm A$是一个$2\times2$的非奇异矩阵, 平面仿射变换有六自由度，对应于六个矩阵元素. 变换可以由三组点对应来计算.
  
  理解仿射变换中线性成分$\mathrm A$的几何效应的一个有益方法是把它看作两个基本变换——旋转和非均匀缩放的复合. 仿射矩阵$\mathrm A$总能分解为$$\mathrm A = \mathrm R(\theta)\mathrm R(-\phi)\mathrm D\mathrm R(\phi) \tag{2.12}$$其中$\mathrm R(\theta)$和$\mathrm R(\phi)$分别表示转角为$\theta$和$\phi$的旋转，而$\mathrm D$为对角矩阵：$$\mathrm D=\begin{bmatrix} \lambda_1 & 0 \\\\ 0 & \lambda_2 \end{bmatrix}$$分解式(2.12)由SVD(节A3.3(p408))(SVD为“奇异值分解”的缩写)直接得到: $\mathrm A=\mathrm U\mathrm D\mathrm V^\intercal=(\mathrm U\mathrm V^\intercal)(\mathrm V\mathrm D\mathrm V^\intercal) = R(\theta)(R(-\phi)\mathrm D\mathrm R(\phi))$, 其中第一个等式来自$\mathrm A$的SVD, 其他 等式则因为$\mathrm U$和$\mathrm V$是正交矩阵.
  
  因此，仿射矩阵$\mathrm A$被看成是一个旋转($\phi$);加上在(已旋转)的$x$和$y$方向分别进行按比例因子$\lambda_1$和$\lambda_2$的缩放，再加上一个回转($-\phi$)和最后一个旋转($\theta$)的复合变换. 与相似变换相比，“新”几何仅仅是非均匀缩放. 它使仿射变换比相似变换多了两个自由度. 它们是缩放方向的角度($\phi$)和缩放参数比率$\lambda_1:\lambda_2$. 仿射变换的本质是在一个特定角的两个垂直方向上进行缩放. 图2.7给出两个示意性例子.
  
  ![image-2.7](/assets/blogImg/2.7.png)
  
  <font size=2>**图2.7  平面仿射变换失真**. (a)旋转R($\theta$). (b)形变R($-\phi$)DR($\phi$). 注意形变中缩放方向是正交的.
  
  </font>
  
  因为仿射变换包含非均匀缩放，所以长度比率和线间夹角等相似不变量在仿射变换下不再保留. 三个重要的**仿射不变量**是：
  
  1. **平行线**  考査两平行线. 它们交于某无穷远点$(x1, x2, 0)^\intercal$. 在仿射变换下，该点被映射到另一个无穷远点. 因此，平行线被映射到仍然交于无穷远的直线，即它们的像仍然平行.
  2. **平行线段的长度比**  直线段的长度缩放仅与该线段方向和缩放方向之间的夹角有关. 假定该线段与正交缩放方向$x-$轴的夹角为$\alpha$, 那么，缩放大小为$\sqrt{\lambda_1^2\cos^2\alpha + \lambda_2^2\sin^2\alpha}$. 因该缩放因子对所有同向的直线是一样的，所以在平行线段的比率中被消去.
  3. **面积比**  可以通过分解式(2.12)直接得到该不变性. 旋转和平移不影响面积，起作用的仅仅是按$\lambda_1$和$\lambda_2$的缩放. 其效果是面积被缩放了$\lambda_1\lambda_2=\mathrm d\mathrm e\mathrm t\;\mathrm A$倍. 因此，任何形状的面积都被缩放$\mathrm d\mathrm e\mathrm t\;\mathrm A$倍，而该缩放因子在面积比中被消去. 我们将会看到这一性质在射影变换时不成立.
  
  根据det A是正或负，仿射变换分别称为保向的或逆向的. 因$\mathrm d\mathrm e\mathrm t\;\mathrm A=\lambda_1\lambda_2$，保向性或逆向性仅与其符号有关.
  
* **射影变换**已在(2.5)中定义. 它是**齐次**坐标的一般非奇异线性变换. 它是仿射变换的推广，仿射变换是**非齐次**坐标的一般非奇异线性变换和一个平移的复合. 我们早已见到射影变换的作用(节2.3). 这里，我们取其分块形式$$\mathbf x'=\mathrm H_\mathrm P \mathbf x=\begin{bmatrix} \mathrm A & \mathbf t \\\\ \mathbf v^\intercal & v \end{bmatrix} \mathbf x \tag{2.13}$$

  其中矢量$\mathbf v= (v_1, v_2)^\intercal$. 齐次矩阵虽有九个元素但只有它们的比率是有意义的，因此该变换由八个参数确定. 注意并不是总有可能通过对矩阵缩放而取$v$为1，因为$v$可能是零. 两平面之间的射影变换可由四组点对应算出，但其中属于同一平面的三点必须不共线. 见图2.4. 与仿射变换不同，在$\mathbb P^2$中不能区分保向或逆向射影变换. 我们将在节2.6中回到这个问题. 最基本的**射影不变量**是四共线点的交比：直线上长度的比率在仿射变换下保持不变，但在射影变换下并非如此. 然而比率的比率即直线上长度的**交比**是射影不变量. 我们将在节2.5讨论这个不变量的性质.
  
* **射影变换可以分解为一串变换链的复合**，链中的每个矩阵比它前面的一个矩阵所表示的变换层次高.$$\mathrm H=\mathrm H_\mathrm S \mathrm H_\mathrm A \mathrm H_\mathrm P=\begin{bmatrix} s\mathrm R & \mathbf t/v \\\\ \mathbf 0^\intercal &1 \end{bmatrix}\begin{bmatrix} \mathrm K & \mathbf 0 \\\\ \mathbf 0^\intercal &1 \end{bmatrix}\begin{bmatrix} \mathrm I & \mathbf 0 \\\\ \mathbf v^\intercal &v \end{bmatrix}=\begin{bmatrix} \mathrm A & \mathbf t \\\\ \mathbf v^\intercal &v \end{bmatrix} \tag{2.16}$$

  其中$\mathrm A=s\mathrm R\mathrm K + \mathbf t\mathbf v^\intercal/v$, 而$\mathrm K$是满足det K = 1的归一化上三角矩阵. 如果$v\neq0$上述分解是有效的，而且如果 $s$ 取正值，它还是唯一的.

  矩阵$\mathrm H_\mathrm S、\mathrm A_\mathrm A、\mathrm H_\mathrm P$分别是相似变换、仿射变换和射影变换(如下标S、A、P所指示). 考察例2.12中对平面的透视图像进行矫正的过程：$\mathrm H_\mathrm P(2\;\mathrm d\mathrm o\mathrm f)$移动无穷远直线；$\mathrm H_\mathrm A(2\;\mathrm d\mathrm o\mathrm f)$影响仿射性质，而不移动无穷远直线；最后，$\mathrm H_\mathrm S$是一般的相似变换(4 dof)，它不影响仿射及射影性质. 变换$\mathrm H_\mathrm P$属于节A5.3(p430)所介绍的一种有约束的透视变换.

* **结论2.16** 与函数无关的不变量数等于或大于配置的自由度数减去变换的自由度数.

  例如，由一般位置上的四点所组成的配置有八个自由度(每点2个)，从而有4个相似，2个仿射和零个射影不变量，因为这些变换分别有4,6,8自由度.

  表2.1归纳了2D变换群以及它们的不变性质. 在表中**低层的变换是它高层的变换的特殊化. 在表低层的变换继承其高层变换的不变量**.

  ![table-2.1](/assets/blogImg/t2.1.png)

  <font size=2>**表2.1.  常见平面变换的几何不变性质**  $\mathrm A = \begin{bmatrix} a_{ij} \end{bmatrix}$是$2\times2$的可逆矩阵，$\mathrm R = \begin{bmatrix} r_{ij} \end{bmatrix}$是2D旋转矩阵，$(t_x, t_y)^\intercal$是2D平移矢量. 失真列给出变换对正方形所产生的典型效应. 表中高层的变换可以产生比它低层的变换的所有效应. 它们的范围从欧式(其中仅有平移和旋转)到射影(其中正方形被变换成任意四边形(假定没有三点共线)). 

* 


