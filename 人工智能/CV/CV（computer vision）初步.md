> 学习路径：卷积神经网络 → Vision Transformer → 目标检测 → 图像分割 → 扩散模型 → CLIP → 视觉语言模型。

---

# 卷积神经网络（CNN）

CNN 是计算机视觉的奠基技术，它模仿人类视觉皮层的工作方式，通过局部感受野提取图像特征。
## 卷积
### 卷积操作原理
卷积的核心思想是：用一个小的滑动窗口（卷积核）在图像上扫描，提取局部特征。比如一个 3×3 的卷积核，每次看图像上的 9 个像素，计算它们的加权和，得到一个输出值。这个过程重复进行，卷积核在图像上从左到右、从上到下滑动，最终生成一张"特征图"。

### 卷积函数的参数
```
def simple_convolution( 
    image: np.ndarray,#传入的图像矩阵
    kernel: np.ndarray#传入的卷积核矩阵；np.ndarray：NumPy 的数组类型
) -> np.ndarray:#表示这个函数预期返回一个 NumPy 数组。
```
###  输出特征图的尺寸 
```python
out_h = img_h - kernel_h + 1
out_w = img_w - kernel_w + 1
```
![[Pasted image 20261010151817.png]]

### 卷积核

####  sobel 垂直边缘检测核
![[Pasted image 20261010150225.png]]
![[Pasted image 20261010150703.png]]
![[Pasted image 20261010150645.png]]
### 卷积核是学习出来的吗![[Pasted image 20261010154700.png]]

### 滑动卷积核
```python
for i in range(out_h):
    for j in range(out_w):# 遍历特征图中的每一个位置
        region = image[
            i:i+kernel_h,# 取出图像中的局部区域（python切片）
            j:j+kernel_w
        ]

        output[i, j] = np.sum(region * kernel)#矩阵相应元素相乘相加得到一个数字
```
### [[卷积#多通道卷积]]
## 实例
```python
\# ============================================  
\# 用 NumPy 实现最简单的卷积操作  
\# 演示卷积如何提取边缘特征  
\# ============================================  
  
import numpy as np  
  
def simple\_convolution(image: np.ndarray, kernel: np.ndarray) -> np.ndarray:  
"""  
实现最基础的 2D 卷积操作（不包含 padding 和 stride）  
  
参数:  
image: 输入图像 (H, W)，单通道灰度图  
kernel: 卷积核 (kH, kW)  
  
返回:  
卷积后的特征图  
"""  
\# 获取图像和卷积核的尺寸  
img\_h, img\_w = image.shape  
kernel\_h, kernel\_w = kernel.shape  
  
\# 计算输出特征图的尺寸  
\# 输出尺寸 = 输入尺寸 - 卷积核尺寸 + 1  
out\_h = img\_h - kernel\_h + 1  
out\_w = img\_w - kernel\_w + 1  
  
\# 初始化输出特征图  
output = np.zeros((out\_h, out\_w))  
  
\# 滑动卷积核进行计算  
for i in range(out\_h):  
for j in range(out\_w):  
\# 取出图像中与卷积核对应的局部区域  
region = image\[i:i+kernel\_h, j:j+kernel\_w\]  
\# 对应元素相乘后求和（这就是卷积操作）  
output\[i, j\] = np.sum(region \* kernel)  
  
return output  
  
\# 创建一个简单的测试图像：中间是白色方块，周围是黑色  
\# 形状：8×8 的灰度图  
test\_image = np.array(\[  
\[0, 0, 0, 0, 0, 0, 0, 0\],  
\[0, 0, 0, 0, 0, 0, 0, 0\],  
\[0, 0, 1, 1, 1, 1, 0, 0\],  
\[0, 0, 1, 1, 1, 1, 0, 0\],  
\[0, 0, 1, 1, 1, 1, 0, 0\],  
\[0, 0, 1, 1, 1, 1, 0, 0\],  
\[0, 0, 0, 0, 0, 0, 0, 0\],  
\[0, 0, 0, 0, 0, 0, 0, 0\],  
\])  
print("原始图像：")  
print(test\_image)  
  
\# 定义一个边缘检测卷积核（Sobel 算子的简化版）  
\# 这个卷积核能检测垂直边缘  
edge\_kernel = np.array(\[  
\[-1, 0, 1\],  
\[-2, 0, 2\],  
\[-1, 0, 1\],  
\])  
  
\# 执行卷积  
feature\_map = simple\_convolution(test\_image, edge\_kernel)  
print("\\n卷积后的特征图（检测到了垂直边缘）：")  
print(np.round(feature\_map, 2))

卷积操作的关键在于：卷积核的参数是学习出来的，不是人工设计的。

在训练过程中，模型会自动调整卷积核的数值，让它能够提取对任务有用的特征。

```

## 池化（Pooling）

池化的作用是压缩特征图的尺寸，减少计算量，同时保持重要特征不变。
#### 最大池化（Max Pooling）
把特征图分成若干小块，每块只保留最大值。
```python
def max_pooling(
    feature_map: np.ndarray,# `feature_map`：卷积得到的特征图。
    pool_size: int = 2#`pool_size=2`：默认使用 \(2\times2\) 的池化窗口。
) -> np.ndarray:
```

### 实例

\# ============================================  
\# 实现最大池化操作  
\# ============================================  
```python
def max\_pooling(feature_map: np.ndarray, pool\_size: int = 2) -> np.ndarray:  
"""  
实现最大池化操作  
  
参数:  
feature\_map: 输入特征图 (H, W)  
pool\_size: 池化窗口大小，默认 2×2  
  
返回:  
池化后的特征图  
"""  
h, w = feature\_map.shape  
\# 计算输出尺寸  
out\_h = h // pool\_size  
out\_w = w // pool\_size  
  
output = np.zeros((out\_h, out\_w))  
  
for i in range(out\_h):  
for j in range(out\_w):  
\# 取出池化窗口区域  
region = feature\_map\[  
i\*pool\_size:(i+1)\*pool\_size,  
j\*pool\_size:(j+1)\*pool\_size  
\]  
\# 取最大值  
output\[i, j\] = np.max(region)  
  
return output  
  
\# 用刚才的特征图做池化  
print("池化前的特征图：")  
print(feature\_map)  
  
pooled = max\_pooling(feature\_map, pool\_size=2)  
print("\\n2×2 最大池化后的特征图：")  
print(pooled)
```
## 卷积与池化的对比
![[Pasted image 20261010154158.png]]

## 经典架构对比

CNN 发展史上有几个里程碑式的架构，它们分别代表了不同时期的设计思路。

| 架构 | 年份 | 核心创新 | 特点 |
| --- | --- | --- | --- |
| AlexNet | 2012 | ReLU 激活、Dropout、数据增强 | 深度学习时代开启者，首次在 ImageNet 上大幅领先传统方法 |
| VGG | 2014 | 统一使用 3×3 小卷积核 | 结构简洁优雅，易于理解和实现 |
| ResNet | 2015 | 残差连接（Skip Connection） | 解决深层网络梯度消失问题，网络可以做到上百层 |
| EfficientNet | 2019 | 复合缩放方法 | 同时缩放深度、宽度和分辨率，参数效率极高 |
# ResNet（Residual Network，残差网络）

## 背景
ResNet（Residual Network，残差网络）是由微软研究院在 2015 年提出的一种深度卷积神经网络。
它主要解决深层 CNN 网络训练困难的问题：
- 梯度消失（Gradient Vanishing）
- 梯度传播困难
- 网络退化问题（Degradation Problem）
在传统 CNN 中：

    Input
      ↓
    Conv
      ↓
    Conv
      ↓
    Conv
      ↓
    Output

随着网络不断加深：
理论上：

> 网络越深，特征提取能力越强，模型性能应该越好。

但是实际训练发现：
- 网络越深，优化越困难；
- 训练误差反而可能增加；
- 深层网络难以学习有效特征。

### 梯度消失（Gradient Vanishing）

神经网络通过反向传播更新参数：

$$
\frac{\partial L}{\partial W}
$$


对于深层网络，梯度需要经过多个网络层传播：

$$
\frac{\partial L}{\partial W}
=
\frac{\partial L}{\partial h_1}
\frac{\partial h_1}{\partial h_2}
...
\frac{\partial h_n}{\partial W}
$$


如果每层梯度小于 1：

$$
0.5^{100}\approx0
$$


会导致：

- 前面网络层梯度接近 0；
- 参数无法有效更新；
- 网络训练困难。
---
### 网络退化问题（Degradation Problem）
理论上：
增加网络深度应该：
- 提升模型表达能力；
- 降低训练误差。
但是实验发现：
更深的网络可能比浅层网络训练误差更高。

例如：
20 层 CNN
和
56 层 CNN
56 层网络性能反而下降。
这个问题不是过拟合，而是：

>当一个深层普通 CNN 需要保持输入特征不变时，它很难通过多层卷积自己学出“什么都不改变”的函数，而 ResNet 通过 shortcut 直接提供恒等映射，使网络只需要学习残差。

---

## 核心思想：残差学习/ 残差连接（Skip Connection）

ResNet 的核心创新是残差连接，它解决了"网络越深越难训练"的问题。

传统网络层的学习目标是：直接学习从输入 x 到输出 y 的映射。

残差网络的学习目标是：学习输出 y 和输入 x 的差值（残差）。

公式表达：y = F(x) + x，其中 F(x) 就是要学习的残差。

> 残差连接的直觉：让网络学习"在现有基础上改多少"，比让它"从零开始学完整映射"要容易得多。

###  残差块（Residual Block）

ResNet 的基本结构：

    输入 x
       |
       |
       ↓
    Conv 3×3
       |
       ↓
      BN
       |
       ↓
     ReLU
       |
       ↓
    Conv 3×3
       |
       ↓
      BN
       |
       ↓
     F(x)
       |
       |
       + <--------- x
       |
       ↓
    输出 y


数学表示：
$$
y=F(x)+x
$$

其中：
- 卷积层学习残差；
- shortcut 保留原始信息；
- 两者相加得到输出。

### 维度不匹配问题

残差相加要求：$F(x)$和$x$维度一致。
例如：
输入$64\times56\times56$输出$128\times28\times28$
两者无法直接相加。解决方法：
####  1×1卷积（Projection Shortcut）

公式：

$$
y=F(x)+W_sx
$$
其中：
$W_s$ 表示通过卷积调整维度。
作用：

- 改变通道数；
- 改变特征图尺寸。

1×1卷积改变通道数

例如：

输入$64$个通道。经过$1\times1$卷积
输出$128$个通道。

#### 降低计算量

ResNet-50 之后使用 Bottleneck 结构：

$$
1\times1
\rightarrow3\times3
\rightarrow1\times1
$$

作用：先降低通道，再进行卷积，最后恢复通道。
例如：

$$
256\rightarrow64\rightarrow64\rightarrow256
$$
## ResNet网络结构

经典 ResNet：

|模型|层数|
|-|-|
|ResNet-18|18层|
|ResNet-34|34层|
|ResNet-50|50层|
|ResNet-101|101层|
|ResNet-152|152层|

其中：

ResNet-50 以上采用 Bottleneck Block：

$$
1\times1
\rightarrow3\times3
\rightarrow1\times1
$$

## ResNet与普通CNN区别

|      | 普通CNN  | ResNet        |
| ---- | ------ | ------------- |
| 学习目标 | $H(x)$ | $F(x)=H(x)-x$ |
| 信息传递 | 逐层传播   | Shortcut跨层传播  |
| 优化难度 | 较高     | 较低            |
| 网络深度 | 有限     | 可以很深          |
| 核心思想 | 学习完整映射 | 学习残差          |

## ResNet在计算机视觉中的应用

ResNet 经常作为视觉任务 Backbone。
应用：

- 图像分类；
- 目标检测；
- 图像分割；
- 人脸识别；
- 遥感目标检测。
典型流程：

    输入图像

        ↓

    ResNet Backbone

        ↓

    提取视觉特征

        ↓

    检测头

        ↓

    类别 + 边界框









## 实例

\# ============================================  
\# 用 PyTorch 实现一个完整的 CNN 图像分类器  
\# 包含卷积、池化、残差连接  
\# ============================================  
```python
import torch  
import torch.nn as nn  
import torch.nn.functional as F  
  
class ResidualBlock(nn.Module):  
"""一个简单的残差块"""  
  
def \_\_init\_\_(self, in\_channels: int, out\_channels: int, stride: int = 1):  
super().\_\_init\_\_()  
\# 第一个卷积层  
self.conv1 = nn.Conv2d(  
in\_channels, out\_channels,  
kernel\_size=3, stride=stride, padding=1, bias=False  
)  
self.bn1 = nn.BatchNorm2d(out\_channels)  
  
\# 第二个卷积层  
self.conv2 = nn.Conv2d(  
out\_channels, out\_channels,  
kernel\_size=3, stride=1, padding=1, bias=False  
)  
self.bn2 = nn.BatchNorm2d(out\_channels)  
  
\# 快捷连接（用于匹配维度变化）  
self.shortcut = nn.Sequential()  
if stride!= 1 or in\_channels!= out\_channels:  
self.shortcut = nn.Sequential(  
nn.Conv2d(  
in\_channels, out\_channels,  
kernel\_size=1, stride=stride, bias=False  
),  
nn.BatchNorm2d(out\_channels)  
)  
  
def forward(self, x: torch.Tensor) -> torch.Tensor:  
\# 主路径：卷积 → BN → ReLU → 卷积 → BN  
out = F.relu(self.bn1(self.conv1(x)))  
out = self.bn2(self.conv2(out))  
  
\# 残差连接：加上输入（或经过维度变换的输入）  
out += self.shortcut(x)  
  
\# 最后的 ReLU  
out = F.relu(out)  
return out  
  
class SimpleCNN(nn.Module):  
"""用于图像分类的简单 CNN（带残差连接）"""  
  
def \_\_init\_\_(self, num\_classes: int = 10):  
super().\_\_init\_\_()  
\# 初始卷积层  
self.conv1 = nn.Conv2d(3, 32, kernel\_size=3, stride=1, padding=1)  
self.bn1 = nn.BatchNorm2d(32)  
  
\# 残差层  
self.layer1 = ResidualBlock(32, 32)  
self.layer2 = ResidualBlock(32, 64, stride=2)  
self.layer3 = ResidualBlock(64, 64)  
  
\# 全局平均池化  
self.global\_pool = nn.AdaptiveAvgPool2d((1, 1))  
  
\# 分类头  
self.fc = nn.Linear(64, num\_classes)  
  
def forward(self, x: torch.Tensor) -> torch.Tensor:  
\# x 形状: (batch\_size, 3, 32, 32)  
x = F.relu(self.bn1(self.conv1(x)))  
  
x = self.layer1(x) # (batch\_size, 32, 32, 32)  
x = self.layer2(x) # (batch\_size, 64, 16, 16)  
x = self.layer3(x) # (batch\_size, 64, 16, 16)  
  
x = self.global\_pool(x) # (batch\_size, 64, 1, 1)  
x = x.view(x.size(0), -1) # (batch\_size, 64)  
x = self.fc(x) # (batch\_size, num\_classes)  
  
return x  
  
\# 创建模型并测试  
model = SimpleCNN(num\_classes=10)  
print("模型结构：")  
print(model)  
  
\# 测试前向传播  
\# 创建一个假的 batch：4 张图片，每张 3×32×32（RGB 32×32）  
test\_input = torch.randn(4, 3, 32, 32)  
test\_output = model(test\_input)  
print(f"\\n输入形状: {test\_input.shape}")  
print(f"输出形状: {test\_output.shape} (batch\_size, num\_classes)")
```

---

