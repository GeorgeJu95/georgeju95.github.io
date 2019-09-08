---
title: 视觉SLAM第三讲——三维空间刚体运动
date: 2019-08-04 14:30:00
categories: 
toc: true
tags: [视觉SLAM十四讲]

---

本章主要讲述如何描述刚体在三维空间中的运动，有四种方式：旋转矩阵、旋转向量(角轴)、四元数、欧拉角，实践部分介绍线性代数库Eigen(一个纯粹用头文件搭建起来的库)

<!-- more -->

## 旋转矩阵

对于$\mathbb R^3$

- 内积：$$\rm \mathbf a\cdot \mathbf b=\mathbf a^\intercal \mathbf b=\sum_{i=1}^3a_ib_i=\bf \left| a \right|\left| b \right| \cos\left <a,b \right>$$内积可以描述向量间的投影关系。
- 外积：$$\rm \mathbf a\times \mathbf b=\begin{Vmatrix} i&j&k \\\\ a_1&a_2&a_3 \\\\ b_1&b_2&b_3 \end{Vmatrix}=\begin{bmatrix} a_2b_3-a_3b_2 \\\\ a_3b_1-a_1b_3 \\\\ a_1b_2-a_2b_1 \end{bmatrix}=\begin{bmatrix} 0&-a_3&a_3 \\\\ a_3&0&-a_1 \\\\ -a_2&a_1&0 \end{bmatrix}\mathbf b=\mathbf a^\land \mathbf b$$ 外积只对三维向量存在定义，能用外积表示向量的旋转。**$\land$可以记成一个反对称符号**，对向量$\bf a$做$\land$运算，把它变成了一个反对称矩阵。
- **旋转矩阵**是一个行列式为1的正交矩阵，反之，行列式为1的正交矩阵也是一个旋转矩阵。所以可以把旋转矩阵的集合定义为**特殊正交群**$$\rm SO(n)=\\{\\, \mathbf R\in\mathbb R^{n\times n}|\mathbf R\mathbf R^\intercal=\mathbf I,\\,det(\mathbf R)=1\\,\\}$$旋转矩阵可以描述相机的旋转，我们用一个旋转矩阵$\mathbf R$和一个平移向量$\bf t$完整的描述了一个欧式空间的坐标变换关系，即$\bf a'=Ra+t$。这样的形式在变换多次后会过于复杂，因此我们引入了<mark>齐次坐标</mark>(三维向量的末尾添加1，将其变为了四维向量)和<mark>变换矩阵</mark>$$\begin{bmatrix} \mathbf a' \\\\ 1\end{bmatrix}=\begin{bmatrix} \mathbf R&\mathbf t \\\\ \mathbf 0^\intercal & 1 \end{bmatrix} \begin{bmatrix} \mathbf a\\\\ 1\end{bmatrix}=\mathbf T\begin{bmatrix} \mathbf a\\\\ 1\end{bmatrix}$$关于变换矩阵$\bf T$，左上角为旋转矩阵，右上角为平移向量，左下角为0向量，右下角为1，这种矩阵又被称为**特殊欧式群**：$$\rm SE(3)=\\{\\,\mathbf T=\begin{bmatrix} \mathbf R&\mathbf t \\\\ \mathbf 0^\intercal & 1 \end{bmatrix} \in\mathbb R^{4\times 4}|\mathbf R\in SO(3), \mathbf t \in \mathbb R^3\\,\\}$$

## 旋转向量

矩阵表示方式有如下缺点：

* SO(3)的旋转矩阵有9个量，但一次旋转只有3个自由度，因此这种表示方式是冗余的。同理，变换矩阵用16个量表达了6自由度的变换。

* 旋转矩阵自身带有约束：它必须是个正交矩阵，且行列式为1.

我们知道，外积可以表示两个向量的旋转关系，任意旋转都可以用一个旋转轴和一个旋转角来刻画。于是引入**旋转向量**来描述旋转，其方向与旋转轴一致，而长度等于旋转角。这种表示只需要一个三维向量即可描述旋转。同样对于变换矩阵，使用一个旋转向量和一个平移向量即可表达一次变换。这时的维数正好是六维。事实上旋转向量就是**李代数**。

#### 罗德里格斯公式：

从旋转向量到旋转矩阵的转换过程由该公式描述：假设有一个旋转轴为$\bf n$，角度为$\theta$的旋转，即它对应的旋转向量为$\theta \bf n$.

$$\bf R=\cos\theta I+(1-\cos\theta)nn^\intercal+\sin\theta n^\land$$反过来也可。对于$\theta$，对公式两边取迹，有：$$\theta=\arccos(\frac{tr(\mathbf R)-1}{2})$$关于转轴$\bf n$，由于旋转轴上的向量在旋转后不发生改变，说明$$\bf Rn=n$$因此，转轴$\bf n$是矩阵$\bf R$特征值为1对应的特征向量。求解此方程，再归一化，就得到了旋转轴。

## 欧拉角

无论是旋转矩阵、旋转向量，它们虽然能描述旋转，但对我们人类是非常不直观的。而欧拉角则提供了一种非常直观的方式来描述旋转——它使用了**3个分离的转角**，把一个旋转分解成3次绕不同轴的旋转。常用的有**ZYX("偏航yaw-俯仰pitch-滚转roll")**。欧拉角有一个重大缺点是会碰到著名的**万向锁问题**(Gimbal Lock)：在俯仰角为±90°时，第一次和第三次旋转将使用同一个轴，使得系统丢失了一个自由度。这被称为**奇异性问题**，理论上可以证明，<u>只要想用3个实数来表达三维旋转时，都会不可避免地碰到奇异性问题</u>。

所以欧拉角不适于插值和迭代，往往只用于人机交互中。也很少在SLAM程序中直接使用欧拉角表达姿态，同样不会再滤波或优化中使用欧拉角表达旋转(因为奇异性)。不过，若想验证自己的算法是否有错，转换成欧拉角能够快速分辨结果是否正确。

## 四元数

<mark>旋转矩阵具有冗余性；欧拉角和旋转向量紧凑，但是具有奇异性。</mark>事实上，我们找不到**不带奇异性的三维向量描述方式**。类似于用复数集表示复平面上的向量，在表达三维空间旋转时，也有一种类似于复数的代数：**四元数**(Quaternion)。它**既是紧凑的，也没有奇异性**。缺点就是不够直观，运算稍微复杂。$$\mathbf q=q_0+q_1i+q_2j+q_3k$$

三个虚部i, j ,k满足：“自己和自己的运算类比复数，自己和别人的运算类比叉乘”

单位四元数能表达三维空间的旋转，角轴(旋转向量)和四元数可以互相转换。假设某个旋转是绕单位向量$\mathbf n=[n_x, n_y, n_z]^\intercal$进行了角度为$\theta$的旋转：$$\mathbf q=[\cos\frac{\theta}{2},n_x\sin\frac{\theta}{2},n_y\sin\frac{\theta}{2},n_z\sin\frac{\theta}{2}]$$反之：$$\begin{equation} \left\\{ \begin{array}{lcl} \theta =2\arccos q_0 \\\\ [n_x,n_y,n_z]^\intercal =[q_1,q_2,q_3]^\intercal/\sin\frac{\theta}{2}\end{array} \right.\end{equation}$$四元数和旋转矩阵的转换以及四元数如何表示旋转见《视觉SLAM十四讲》P55，四元数的运算见P53。