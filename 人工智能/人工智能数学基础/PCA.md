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
