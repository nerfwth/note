## 一、PCA 是什么？为什么需要它？

假设你收集了一个数据集，每个样本都有很多特征。

例如，要描述一名学生，可以使用：

- 数学成绩
- 物理成绩
- 英语成绩

那么每名学生可以表示为三维向量：

$$
x_i=
\begin{bmatrix}
x_{i1}\\
x_{i2}\\
x_{i3}
\end{bmatrix}
$$

如果有 $n$ 名学生，就有 $n$ 个三维向量。

现在假设数学成绩和物理成绩高度相关：数学成绩高的学生，物理成绩通常也高。

这意味着，数据虽然有三个特征，但可能没有真正需要三个维度才能描述的信息。

PCA 的核心思想：寻找数据变化最大的几个方向，再把数据投影到这些方向上，从而实现降维。

例如：

![Applications of Principal Component Analysis (PCA) – QuantBender](https://images.openai.com/static-rsc-4/-OAWBPBvqJdnbycF_fISYYHGZpnECfqGrc5p1xKcw1bCivseDGOpurFVX6uD5ynzA3pNOKyXLm7JGtLBopIdBpIseNHKrOBy0386iX0Zb7md1E-DInu_JfdsDG0AqyvSbmvSt2FapYbZlek1rqoyOeYwjFMmTd5H2U6OPGJex-g?purpose=inline)

原始数据

每个样本有两个特征，数据主要沿着一个斜方向分布。

![Principal Component Analysis, Part 2 — Algorithms for Data Science](https://images.openai.com/static-rsc-4/twljGsshDu0_O-rHqrCapHWTQhynXNLsjJpw5Nc6jVvM3ejUNavj7HwJOtv-ZbohR0f1a3gEpFmhnecmJRzPvyV8r6zGN-mYE1jAILY9vgSLassDjsyL_tVzDRXERgnCuqXUYsEfGbzSeISdP2uJwEIYU47mUD6E2I4DEwUBiJY?purpose=inline)

PCA 降维

找到主要变化方向，将二维样本投影到一条直线上，用一个数近似表示每个样本。

因此，PCA 可以把二维数据降到一维，也可以把几百维的特征降到几十维。

它的目标不是随便删除一些特征，而是尽可能保留数据中重要的变化信息。

## 二、PCA 的核心：什么叫“变化最大”？

这是理解 PCA 的关键。

假设我们有二维数据，想把每个点投影到某个方向上。

设投影方向是单位向量：

$$
v=
\begin{bmatrix}
v_1\\
v_2
\end{bmatrix},
\qquad
\|v\|_2=1
$$

某个样本为：

$$
x_i=
\begin{bmatrix}
x_{i1}\\
x_{i2}
\end{bmatrix}
$$

那么，样本在方向 $v$ 上的投影坐标就是：

$$
\boxed{z_i=v^Tx_i}
$$

为什么是内积？

因为 $v$ 是单位向量， $v^Tx_i$ 就是 $x_i$ 在方向 $v$ 上的有符号投影长度。

例如：

$$
v=\begin{bmatrix}1\\0\end{bmatrix}
$$

那么：

$$
z_i=
\begin{bmatrix}1&0\end{bmatrix}
\begin{bmatrix}x_{i1}\\x_{i2}\end{bmatrix}
=x_{i1}
$$

这就是投影到水平方向，保留第一个坐标。

如果：

$$
v=\frac{1}{\sqrt2}
\begin{bmatrix}1\\1\end{bmatrix}
$$

那么：

$$
z_i=\frac{x_{i1}+x_{i2}}{\sqrt2}
$$

这就相当于沿着 $45^\circ$ 方向提取信息。

那么问题来了：方向 $v$ 有无穷多个，我们到底应该选哪个？

PCA 的答案是：

$$
\boxed{\text{选择投影后方差最大的方向}}
$$

为什么选择方差最大？

- 如果投影后的数据都挤在一个位置附近，样本之间就很难区分。
- 如果投影后的数据分布较开，通常说明这个方向保留了更多的数据差异。

所以，第一主成分就是使投影方差最大的单位方向。

## 三、从投影方差推导 PCA 的优化公式

这部分是本节最重要的数学推导。

## 3.1 先对数据中心化

假设原始数据集有 $n$ 个样本，每个样本有 $p$ 个特征。

把所有样本按行排列，得到数据矩阵：

$$
X=
\begin{bmatrix}
x_1^T\\
x_2^T\\
\vdots\\
x_n^T
\end{bmatrix}
\in\mathbb R^{n\times p}
$$

这里先假设每个特征的均值已经减去，因此：

$$
\boxed{\frac1n\sum_{i=1}^{n}x_i=0}
$$

为什么要中心化？

因为我们希望研究的是数据围绕均值的变化方向，而不是数据整体位于哪里。

记中心化后的数据矩阵仍为 $X$ 。

## 3.2 计算投影坐标

设投影方向为单位向量：

$$
v\in\mathbb R^p,\qquad \|v\|_2=1
$$

每个样本的投影坐标是：

$$
z_i=v^Tx_i
$$

所有样本的投影坐标可以合并成向量：

$$
z=
\begin{bmatrix}
z_1\\
z_2\\
\vdots\\
z_n
\end{bmatrix}
$$

因为 $z_i=v^Tx_i=x_i^Tv$ ，所以：

$$
\boxed{z=Xv}
$$

注意维度：

$$
(n\times p)(p\times1)=n\times1
$$

这说明原来 $p$ 维的每个样本，投影后都变成了一个数。

## 3.3 推导投影方差

由于数据已经中心化，投影坐标的均值也是零。

因此，投影方差为：

$$
\operatorname{Var}(z)
=\frac1n\sum_{i=1}^{n}z_i^2
$$

把所有投影坐标写成向量：

$$
\operatorname{Var}(z)=\frac1n z^Tz
$$

代入：

$$
z=Xv
$$

得到：

$$
\operatorname{Var}(z)
=\frac1n(Xv)^T(Xv)
$$

利用矩阵转置的性质：

$$
(Xv)^T=v^TX^T
$$

所以：

$$
\operatorname{Var}(z)
=\frac1n v^TX^TXv
$$

最终得到：

$$
\boxed{
\operatorname{Var}(z)
=\frac1n v^TX^TXv
}
$$

如果定义协方差矩阵：

$$
\boxed{C=\frac1nX^TX}
$$

那么：

$$
\boxed{\operatorname{Var}(z)=v^TCv}
$$

因此，寻找第一主成分就变成了一个数学优化问题：

$$
\boxed{
\begin{aligned}
\max_v\quad &v^TCv\\
\text{s.t.}\quad &v^Tv=1
\end{aligned}
}
$$

也就是说，在所有单位方向中，找出使投影方差最大的那个方向。

## 四、为什么最大方差方向恰好是最大特征值对应的特征向量？

终于到了你前面学习的特征分解发挥作用的地方。

由于：

$$
C=\frac1nX^TX
$$

所以 $C$ 是实对称矩阵，而且是半正定矩阵。

因此可以进行谱分解：

$$
\boxed{C=V\Lambda V^T}
$$

其中：

$$
\Lambda=
\operatorname{diag}(\lambda_1,\lambda_2,\ldots,\lambda_p)
$$

并且按照从大到小排列：

$$
\lambda_1\geq\lambda_2\geq\cdots\geq\lambda_p\geq0
$$

设 $v_1,\ldots,v_p$ 是对应的标准正交特征向量。

为了避免与投影方向的符号混淆，接下来用 $w$ 表示任意候选投影方向。

因为 $v_1,\ldots,v_p$ 构成标准正交基，任意单位向量 $w$ 都可以写成：

$$
w=\sum_{j=1}^{p}a_jv_j
$$

其中：

$$
\sum_{j=1}^{p}a_j^2=1
$$

把它代入方差目标：

$$
w^TCw
=
\left(\sum_i a_iv_i\right)^T
C
\left(\sum_j a_jv_j\right)
$$

由于：

$$
Cv_j=\lambda_jv_j
$$

且：

$$
v_i^Tv_j=
\begin{cases}
1,&i=j\\
0,&i\neq j
\end{cases}
$$

所有交叉项都消失，最终得到：

$$
\boxed{w^TCw=\sum_{j=1}^{p}\lambda_ja_j^2}
$$

又因为：

$$
\lambda_j\leq\lambda_1
$$

所以：

$$
\sum_{j=1}^{p}\lambda_ja_j^2
\leq
\lambda_1\sum_{j=1}^{p}a_j^2
$$

利用：

$$
\sum_{j=1}^{p}a_j^2=1
$$

得到：

$$
\boxed{w^TCw\leq\lambda_1}
$$

什么时候等号成立？

当 $w=v_1$ 时， $a_1=1$ ，其他系数全为零。因此：

$$
w^TCw=\lambda_1
$$

于是我们严格证明了：

$$
\boxed{
\max_{\|w\|_2=1}w^TCw=\lambda_1
}
$$

而达到最大值的方向就是 $C$ 最大特征值对应的特征向量。

这就是 PCA 为什么要进行特征值分解的原因。

- 最大特征值 $\lambda_1$ ：第一主成分所保留的方差。
- 对应特征向量 $v_1$ ：第一主成分的方向。

第二主成分则是在与第一主成分正交的条件下，使剩余投影方差最大的方向，对应第二大特征值的特征向量 $v_2$ 。其余主成分依此类推。

第3章矩阵的分解及应用(1).pdf

## 五、如何用 PCA 实现降维？

假设原始数据有 $p$ 个特征，但我们只想保留 $k$ 个维度，其中：

$$
k<p
$$

先求出协方差矩阵的特征分解：

$$
C=V\Lambda V^T
$$

把前 $k$ 个特征值对应的特征向量取出来：

$$
V_k=[v_1,v_2,\ldots,v_k]
$$

其中：

$$
V_k\in\mathbb R^{p\times k}
$$

然后将数据投影到这 $k$ 个主成分方向上：

$$
\boxed{Z=XV_k}
$$

维度变化为：

$$
(n\times p)(p\times k)=n\times k
$$

原来的数据矩阵是：

$$
X\in\mathbb R^{n\times p}
$$

降维后的数据矩阵是：

$$
Z\in\mathbb R^{n\times k}
$$

这就是 PCA 的降维过程。

## 如何判断保留多少个主成分？

通常计算每个主成分的解释方差比例：

$$
\boxed{
r_i=\frac{\lambda_i}{\sum_{j=1}^{p}\lambda_j}
}
$$

前 $k$ 个主成分的累计解释方差比例为：

$$
\boxed{
R_k=\frac{\sum_{i=1}^{k}\lambda_i}
{\sum_{j=1}^{p}\lambda_j}
}
$$

例如，如果前两个主成分的累计解释方差比例为 $95\%$ ，就意味着它们保留了原始数据总方差的 $95\%$ 。

实际中，可以根据任务选择合适的 $k$ ，而不是一定取某个固定值。

# PCA 中的方差、协方差矩阵与投影方差

## 一、什么是方差？

方差用来衡量一组数据偏离均值的程度。

假设有一组数据：

$$
x=[60,70,80,90]
$$

首先计算均值：

$$
\bar{x}=\frac{60+70+80+90}{4}=75
$$

每个数据与均值的偏差为：

$$
[-15,-5,5,15]
$$

将偏差平方，再求平均：

$$
\begin{aligned}
\operatorname{Var}(x)
&=\frac{1}{4}\sum_{i=1}^{4}(x_i-\bar{x})^2\\
&=\frac{(-15)^2+(-5)^2+5^2+15^2}{4}\\
&=125
\end{aligned}
$$

因此，总体方差的定义为：

$$
\boxed{
\operatorname{Var}(x)=
\frac{1}{n}\sum_{i=1}^{n}(x_i-\bar{x})^2
}
$$

### 方差的直观意义

- 方差大：数据比较分散。
- 方差小：数据比较集中。
- 方差为零：所有数据都相等。

注意：这里采用除以 $n$ 的定义。如果使用样本方差，通常除以 $n-1$。

---

## 二、什么是协方差？

方差研究的是一个变量自身的变化。

如果有两个变量，我们还想知道它们是否倾向于一起变化。

例如，研究数学成绩和物理成绩：

| 学生 | 数学成绩 | 物理成绩 |
|---|---:|---:|
| A | 60 | 65 |
| B | 70 | 75 |
| C | 80 | 85 |
| D | 90 | 95 |

数学成绩升高时，物理成绩也升高，两个变量呈现同向变化趋势。

### 2.1 协方差的定义

设两个变量为 $x$ 和 $y$，其均值分别为 $\bar{x}$ 和 $\bar{y}$。

协方差定义为：

$$
\boxed{
\operatorname{Cov}(x,y)=
\frac{1}{n}\sum_{i=1}^{n}
(x_i-\bar{x})(y_i-\bar{y})
}
$$

与方差相比，两者的区别是：

- 方差：偏差乘以自身偏差。
- 协方差：一个变量的偏差乘以另一个变量的偏差。

### 2.2 协方差的正负

- $\operatorname{Cov}(x,y)>0$：两个变量倾向于同向变化。
- $\operatorname{Cov}(x,y)<0$：两个变量倾向于反向变化。
- $\operatorname{Cov}(x,y)=0$：两个变量的线性共同变化为零。

注意：协方差为零不一定意味着两个变量相互独立。

---

## 三、什么是协方差矩阵？

当数据具有多个特征时，我们既要计算每个特征自身的方差，也要计算不同特征之间的协方差。

把这些数排列成矩阵，就得到协方差矩阵。

对于两个特征：

$$
\boxed{
C=
\begin{bmatrix}
\operatorname{Var}(x_1)&\operatorname{Cov}(x_1,x_2)\\
\operatorname{Cov}(x_2,x_1)&\operatorname{Var}(x_2)
\end{bmatrix}
}
$$

### 3.1 协方差矩阵的结构

- 主对角线：各个特征自身的方差。
- 非对角线：不同特征之间的协方差。

协方差矩阵具有两个重要性质：

**性质一：对称性**

$$
C^T=C
$$

因为：

$$
\operatorname{Cov}(x_1,x_2)
=
\operatorname{Cov}(x_2,x_1)
$$

**性质二：半正定性**

对于任意向量 $v$：

$$
v^TCv\geq 0
$$

因此，协方差矩阵的特征值均为非负数。

这也是 PCA 可以对协方差矩阵进行谱分解的重要原因。

---

## 四、如何计算协方差矩阵？

假设有 $n$ 个样本，每个样本有 $p$ 个特征。

将样本按行排列，得到数据矩阵：

$$
X=
\begin{bmatrix}
x_1^T\\
x_2^T\\
\vdots\\
x_n^T
\end{bmatrix}
\in\mathbb{R}^{n\times p}
$$

先对数据进行中心化，即每个特征减去自身的均值。

中心化后，每一列的均值都为零。

此时，协方差矩阵可以写成：

$$
\boxed{C=\frac{1}{n}X^TX}
$$

### 为什么是 $X^TX$？

因为 $X$ 的每一列对应一个特征。

矩阵乘积 $X^TX$ 的元素为：

$$
(X^TX)_{ij}
=
\sum_{k=1}^{n}x_{ki}x_{kj}
$$

由于数据已经中心化，这就是第 $i$ 个特征与第 $j$ 个特征的离差乘积之和。

当 $i=j$ 时：

$$
(X^TX)_{ii}
=
\sum_{k=1}^{n}x_{ki}^2
$$

它对应第 $i$ 个特征的离差平方和。

当 $i\neq j$ 时：

$$
(X^TX)_{ij}
=
\sum_{k=1}^{n}x_{ki}x_{kj}
$$

它对应两个不同特征的离差乘积之和。

因此，除以 $n$ 后：

$$
\boxed{C=\frac{1}{n}X^TX}
$$

就同时得到了所有特征的方差和协方差。

---

## 五、什么是各方向上的方差？

这是 PCA 中最重要的概念之一。

前面计算的方差，是沿原始坐标轴方向的方差。

例如，二维数据有两个特征：

- 水平方向：第一个特征的方向。
- 竖直方向：第二个特征的方向。

但我们也可以研究任意斜着的方向。

**某个方向上的方差，就是把所有样本投影到这个方向上，再计算投影坐标的方差。**

### 5.1 设定投影方向

设投影方向为单位向量：

$$
v=
\begin{bmatrix}
a\\
b
\end{bmatrix}
$$

满足：

$$
\boxed{v^Tv=a^2+b^2=1}
$$

例如：

水平方向：

$$
v=
\begin{bmatrix}
1\\
0
\end{bmatrix}
$$

竖直方向：

$$
v=
\begin{bmatrix}
0\\
1
\end{bmatrix}
$$

$45^\circ$ 方向：

$$
v=\frac{1}{\sqrt{2}}
\begin{bmatrix}
1\\
1
\end{bmatrix}
$$

### 5.2 计算投影坐标

设某个中心化样本为 $x_i$。

它在方向 $v$ 上的投影坐标为：

$$
\boxed{z_i=v^Tx_i}
$$

为什么使用内积？

因为 $v$ 是单位向量，所以 $v^Tx_i$ 就是样本在该方向上的有符号投影长度。

将所有投影坐标排列起来：

$$
z=
\begin{bmatrix}
z_1\\
z_2\\
\vdots\\
z_n
\end{bmatrix}
$$

由于数据矩阵 $X$ 的样本按行排列，因此：

$$
\boxed{z=Xv}
$$

维度为：

$$
(n\times p)(p\times 1)=n\times 1
$$

也就是说，每个原本具有 $p$ 个特征的样本，投影后变成一个数。

### 5.3 推导投影方差公式

因为数据已经中心化，所以投影坐标的均值也是零。

因此：

$$
\operatorname{Var}(z)
=
\frac{1}{n}\sum_{i=1}^{n}z_i^2
$$

写成矩阵形式：

$$
\operatorname{Var}(z)=\frac{1}{n}z^Tz
$$

代入 $z=Xv$：

$$
\operatorname{Var}(z)
=
\frac{1}{n}(Xv)^T(Xv)
$$

利用转置公式：

$$
(Xv)^T=v^TX^T
$$

得到：

$$
\operatorname{Var}(z)
=
\frac{1}{n}v^TX^TXv
$$

又因为：

$$
C=\frac{1}{n}X^TX
$$

所以：

$$
\boxed{\operatorname{Var}(Xv)=v^TCv}
$$

这是 PCA 计算任意单位方向上投影方差的核心公式。

---

## 六、投影方差为什么等于特征值？

需要注意：**只有当投影方向是协方差矩阵的单位特征向量时，投影方差才等于对应的特征值。**

设 $v$ 是协方差矩阵 $C$ 的单位特征向量，对应特征值为 $\lambda$。

根据特征向量的定义：

$$
Cv=\lambda v
$$

投影方差为：

$$
\operatorname{Var}(Xv)=v^TCv
$$

代入 $Cv=\lambda v$：

$$
\begin{aligned}
\operatorname{Var}(Xv)
&=v^T(\lambda v)\\
&=\lambda v^Tv
\end{aligned}
$$

由于 $v$ 是单位向量：

$$
v^Tv=1
$$

因此：

$$
\boxed{\operatorname{Var}(Xv)=\lambda}
$$

这说明：

- 特征值：对应主成分方向上的投影方差。
- 特征向量：对应主成分的方向。

注意：对于任意单位方向 $v$，投影方差仍然是 $v^TCv$，但不一定等于某个特征值。

---

## 七、PCA 为什么选择最大特征值对应的特征向量？

PCA 的第一主成分要使投影方差最大。

因此，其优化问题为：

$$
\boxed{
\begin{aligned}
\max_v\quad &v^TCv\\
\text{s.t.}\quad &v^Tv=1
\end{aligned}
}
$$

由于 $C$ 是实对称矩阵，可以进行特征值分解：

$$
C=Q\Lambda Q^T
$$

设特征值从大到小排列：

$$
\lambda_1\geq\lambda_2\geq\cdots\geq\lambda_p\geq0
$$

将任意单位向量 $v$ 表示为特征向量的线性组合：

$$
v=\sum_{i=1}^{p}a_iq_i
$$

由于特征向量标准正交，并且 $v$ 是单位向量：

$$
\sum_{i=1}^{p}a_i^2=1
$$

代入投影方差：

$$
\begin{aligned}
v^TCv
&=\left(\sum_i a_iq_i\right)^T
C\left(\sum_j a_jq_j\right)\\
&=\sum_{i=1}^{p}\lambda_i a_i^2
\end{aligned}
$$

因为所有特征值都不超过 $\lambda_1$：

$$
\begin{aligned}
v^TCv
&=\sum_{i=1}^{p}\lambda_i a_i^2\\
&\leq\lambda_1\sum_{i=1}^{p}a_i^2\\
&=\lambda_1
\end{aligned}
$$

当 $v=q_1$ 时，投影方差达到最大值 $\lambda_1$。

因此：

$$
\boxed{
\max_{v^Tv=1}v^TCv=\lambda_1
}
$$

**第一主成分就是最大特征值对应的单位特征向量。**

第二主成分则是在与第一主成分正交的条件下，使投影方差最大的方向，对应第二大特征值的特征向量。

---

## 八、完整例题：计算不同方向上的方差

考虑中心化后的数据矩阵：

$$
X=
\begin{bmatrix}
2&1\\
1&2\\
-1&-2\\
-2&-1
\end{bmatrix}
$$

### 8.1 计算协方差矩阵

$$
C=\frac{1}{4}X^TX
$$

计算得：

$$
X^TX=
\begin{bmatrix}
10&8\\
8&10
\end{bmatrix}
$$

因此：

$$
\boxed{
C=
\begin{bmatrix}
2.5&2\\
2&2.5
\end{bmatrix}
}
$$

其中：

- 第一个特征的方差为 $2.5$。
- 第二个特征的方差为 $2.5$。
- 两个特征之间的协方差为 $2$。

### 8.2 水平方向上的方差

取：

$$
v_1=
\begin{bmatrix}
1\\
0
\end{bmatrix}
$$

使用公式：

$$
\operatorname{Var}(Xv_1)=v_1^TCv_1
$$

得到：

$$
\boxed{\operatorname{Var}(Xv_1)=2.5}
$$

### 8.3 $45^\circ$ 方向上的方差

取：

$$
v_2=\frac{1}{\sqrt{2}}
\begin{bmatrix}
1\\
1
\end{bmatrix}
$$

计算：

$$
Cv_2=
\frac{1}{\sqrt{2}}
\begin{bmatrix}
4.5\\
4.5
\end{bmatrix}
$$

因此：

$$
\begin{aligned}
v_2^TCv_2
&=\frac{1}{2}(4.5+4.5)\\
&=4.5
\end{aligned}
$$

所以：

$$
\boxed{\operatorname{Var}(Xv_2)=4.5}
$$

### 8.4 $-45^\circ$ 方向上的方差

取：

$$
v_3=\frac{1}{\sqrt{2}}
\begin{bmatrix}
1\\
-1
\end{bmatrix}
$$

计算：

$$
Cv_3=
\frac{1}{\sqrt{2}}
\begin{bmatrix}
0.5\\
-0.5
\end{bmatrix}
$$

因此：

$$
\boxed{\operatorname{Var}(Xv_3)=0.5}
$$

### 8.5 结果总结

| 投影方向 | 投影方差 |
|---|---:|
| 水平 | $2.5$ |
| $45^\circ$ | $4.5$ |
| $-45^\circ$ | $0.5$ |

因此，$45^\circ$ 方向的投影方差最大。

对应的单位特征向量为：

$$
v_2=\frac{1}{\sqrt{2}}
\begin{bmatrix}
1\\
1
\end{bmatrix}
$$

对应的特征值为：

$$
\lambda_1=4.5
$$

它就是第一主成分方向。

---

## 九、方差贡献率是什么？

PCA 不仅要寻找主成分，还需要判断保留多少个主成分。

协方差矩阵的全部特征值之和等于总方差：

$$
\boxed{
\operatorname{tr}(C)=\sum_{i=1}^{p}\lambda_i
}
$$

第 $i$ 个主成分的方差贡献率为：

$$
\boxed{
r_i=\frac{\lambda_i}{\sum_{j=1}^{p}\lambda_j}
}
$$

前 $k$ 个主成分的累计方差贡献率为：

$$
\boxed{
R_k=\frac{\sum_{i=1}^{k}\lambda_i}
{\sum_{j=1}^{p}\lambda_j}
}
$$

在上面的例题中：

$$
\lambda_1=4.5,\qquad\lambda_2=0.5
$$

总方差为：

$$
4.5+0.5=5
$$

第一主成分的方差贡献率为：

$$
r_1=\frac{4.5}{5}=90\%
$$

第二主成分的方差贡献率为：

$$
r_2=\frac{0.5}{5}=10\%
$$

如果只保留第一主成分，就相当于从二维降到一维，并保留总方差的 $90\%$。

---

## 十、核心公式总结

### 方差

$$
\operatorname{Var}(x)=
\frac{1}{n}\sum_{i=1}^{n}(x_i-\bar{x})^2
$$

### 协方差

$$
\operatorname{Cov}(x,y)=
\frac{1}{n}\sum_{i=1}^{n}
(x_i-\bar{x})(y_i-\bar{y})
$$

### 协方差矩阵

中心化数据矩阵为 $X$ 时：

$$
C=\frac{1}{n}X^TX
$$

### 投影坐标

$$
z=Xv
$$

### 任意方向上的投影方差

$$
\boxed{\operatorname{Var}(Xv)=v^TCv}
$$

### 单位特征向量方向上的投影方差

$$
Cv=\lambda v,\qquad v^Tv=1
$$

因此：

$$
\boxed{\operatorname{Var}(Xv)=\lambda}
$$

### PCA 的优化目标

$$
\max_{v^Tv=1}v^TCv
$$

最大值为：

$$
\boxed{\lambda_{\max}(C)}
$$

### 方差贡献率

$$
r_i=\frac{\lambda_i}{\sum_j\lambda_j}
$$

**最终理解：PCA 通过协方差矩阵研究数据在不同方向上的变化程度，再利用特征值分解，找到投影方差最大的方向，从而实现降维。**

## 六、PCA 和 SVD 有什么关系？

这是你目前学习进度中特别重要的连接点。

假设中心化数据矩阵进行 SVD：

$$
\boxed{X=U\Sigma V^T}
$$

那么：

$$
X^TX
=
(U\Sigma V^T)^T(U\Sigma V^T)
$$

利用转置公式：

$$
X^TX=V\Sigma^TU^TU\Sigma V^T
$$

由于 $U$ 的列向量标准正交：

$$
U^TU=I
$$

所以：

$$
\boxed{X^TX=V\Sigma^2V^T}
$$

因此，协方差矩阵为：

$$
C=\frac1nX^TX
=\boxed{V\frac{\Sigma^2}{n}V^T}
$$

对比特征分解：

$$
C=V\Lambda V^T
$$

可知：

$$
\boxed{\lambda_i=\frac{\sigma_i^2}{n}}
$$

这里假设使用的是除以 $n$ 的协方差定义；如果使用 $n-1$ ，特征值会相应变化，但主成分方向不变。

这意味着：

- $V$ 的列向量就是 PCA 的主成分方向。
- 奇异值的平方除以 $n$ ，就是对应主成分的方差。
- 投影后的数据为：
$$
Z=XV_k=U_k\Sigma_k
$$

这正是你课件中从 SVD 推导 PCA 的核心关系。

第3章矩阵的分解及应用(1).pdf

## 本节总结

PCA 的优化目标

$$
\max_{\|v\|_2=1}v^TCv
$$

主成分的求法

$$
Cv_i=\lambda_i v_i
$$

按特征值从大到小排列，取前几个特征向量。

降维公式

$$
Z=XV_k
$$

与 SVD 的关系

$$
X=U\Sigma V^T
$$
 
$$
\lambda_i=\frac{\sigma_i^2}{n}
$$
