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

## 残差连接（Skip Connection）

ResNet 的核心创新是残差连接，它解决了"网络越深越难训练"的问题。

传统网络层的学习目标是：直接学习从输入 x 到输出 y 的映射。

残差网络的学习目标是：学习输出 y 和输入 x 的差值（残差）。

公式表达：y = F(x) + x，其中 F(x) 就是要学习的残差。

> 残差连接的直觉：让网络学习"在现有基础上改多少"，比让它"从零开始学完整映射"要容易得多。

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

## Vision Transformer（ViT）

2020 年，Google 发表论文《An Image is Worth 16x16 Words》，把 Transformer 架构从语言领域搬到了视觉领域。

### 图像分块（Patch Embedding）

Transformer 处理的是序列数据，但图像是 2D 的网格数据。

ViT 的解决方案很简单：把图像切成一个个小块，每个小块当作一个词。

比如一张 224×224 的图像，切成 16×16 的小块，就得到 14×14 = 196 个 patch。

每个 patch 展平成一维向量，再通过线性层投影成 embedding，就可以送入 Transformer 了。

## 实例

\# ============================================  
\# 实现 Patch Embedding（图像分块）  
\# ============================================  
```
 
class PatchEmbedding(nn.Module):  
"""  
将图像分割成 patch 并嵌入  
  
参数:  
img\_size: 输入图像大小（正方形）  
patch\_size: 每个 patch 的大小（正方形）  
in\_channels: 输入通道数（RGB 是 3）  
embed\_dim: 输出嵌入维度  
"""  
  
def \_\_init\_\_(  
self,  
img\_size: int = 224,  
patch\_size: int = 16,  
in\_channels: int = 3,  
embed\_dim: int = 768  
):  
super().\_\_init\_\_()  
self.img\_size = img\_size  
self.patch\_size = patch\_size  
\# 计算 patch 数量：(224/16)^2 = 14^2 = 196  
self.num\_patches = (img\_size // patch\_size) \*\* 2  
  
\# 用卷积实现 patch 划分和嵌入  
\# 这等价于：切 patch → 展平 → 线性投影  
self.proj = nn.Conv2d(  
in\_channels, embed\_dim,  
kernel\_size=patch\_size, stride=patch\_size  
)  
  
def forward(self, x: torch.Tensor) -> torch.Tensor:  
\# x 形状: (batch\_size, in\_channels, img\_size, img\_size)  
x = self.proj(x) # (batch\_size, embed\_dim, num\_patches^0.5, num\_patches^0.5)  
x = x.flatten(2) # (batch\_size, embed\_dim, num\_patches)  
x = x.transpose(1, 2) # (batch\_size, num\_patches, embed\_dim)  
return x  
  
\# 测试 Patch Embedding  
patch\_embed = PatchEmbedding(img\_size=224, patch\_size=16, embed\_dim=768)  
test\_image = torch.randn(1, 3, 224, 224) # 1 张 224×224 的 RGB 图  
patches = patch\_embed(test\_image)  
print(f"输入图像形状: {test\_image.shape}")  
print(f"Patch 序列形状: {patches.shape} (batch\_size, num\_patches, embed\_dim)")
```


### ViT 完整架构

ViT 的完整架构还需要加上 class token 和位置编码。

class token 是一个可学习的向量，它的作用是"汇总"所有 patch 的信息。

位置编码也是可学习的，它告诉模型"每个 patch 在图像的哪个位置"。

## 实例

\# ============================================  
\# 实现一个简化版的 Vision Transformer  
\# ============================================  
```python
  
class MultiHeadAttention(nn.Module):  
"""多头自注意力"""  
  
def \_\_init\_\_(self, dim: int, num\_heads: int):  
super().\_\_init\_\_()  
self.num\_heads = num\_heads  
self.head\_dim = dim // num\_heads  
assert self.head\_dim \* num\_heads == dim, "维度必须能被头数整除"  
  
\# Q、K、V 投影矩阵  
self.qkv = nn.Linear(dim, dim \* 3)  
\# 输出投影  
self.proj = nn.Linear(dim, dim)  
  
def forward(self, x: torch.Tensor) -> torch.Tensor:  
batch\_size, seq\_len, dim = x.shape  
  
\# 计算 Q、K、V  
qkv = self.qkv(x) # (batch\_size, seq\_len, dim\*3)  
qkv = qkv.reshape(  
batch\_size, seq\_len, 3, self.num\_heads, self.head\_dim  
) # (batch\_size, seq\_len, 3, num\_heads, head\_dim)  
q, k, v = qkv.unbind(2) # 每个形状: (batch\_size, seq\_len, num\_heads, head\_dim)  
  
\# 调整顺序以便计算注意力  
q = q.transpose(1, 2) # (batch\_size, num\_heads, seq\_len, head\_dim)  
k = k.transpose(1, 2)  
v = v.transpose(1, 2)  
  
\# 计算注意力分数  
attention = q @ k.transpose(-2, -1) / (self.head\_dim \*\* 0.5)  
attention = attention.softmax(dim=-1)  
  
\# 聚合值  
out = attention @ v # (batch\_size, num\_heads, seq\_len, head\_dim)  
out = out.transpose(1, 2) # (batch\_size, seq\_len, num\_heads, head\_dim)  
out = out.flatten(2) # (batch\_size, seq\_len, dim)  
  
\# 输出投影  
out = self.proj(out)  
return out  
  
class TransformerBlock(nn.Module):  
"""一个 Transformer 块"""  
  
def \_\_init\_\_(self, dim: int, num\_heads: int, mlp\_ratio: float = 4.0):  
super().\_\_init\_\_()  
self.norm1 = nn.LayerNorm(dim)  
self.attn = MultiHeadAttention(dim, num\_heads)  
self.norm2 = nn.LayerNorm(dim)  
  
\# MLP  
mlp\_hidden = int(dim \* mlp\_ratio)  
self.mlp = nn.Sequential(  
nn.Linear(dim, mlp\_hidden),  
nn.GELU(),  
nn.Linear(mlp\_hidden, dim)  
)  
  
def forward(self, x: torch.Tensor) -> torch.Tensor:  
\# 残差连接 1  
x = x + self.attn(self.norm1(x))  
\# 残差连接 2  
x = x + self.mlp(self.norm2(x))  
return x  
  
class SimpleViT(nn.Module):  
"""简化版 Vision Transformer"""  
  
def \_\_init\_\_(  
self,  
img\_size: int = 224,  
patch\_size: int = 16,  
in\_channels: int = 3,  
num\_classes: int = 10,  
embed\_dim: int = 192,  
depth: int = 6,  
num\_heads: int = 6  
):  
super().\_\_init\_\_()  
\# Patch 嵌入  
self.patch\_embed = PatchEmbedding(  
img\_size, patch\_size, in\_channels, embed\_dim  
)  
num\_patches = self.patch\_embed.num\_patches  
  
\# Class token  
self.cls\_token = nn.Parameter(torch.zeros(1, 1, embed\_dim))  
  
\# 位置编码  
self.pos\_embed = nn.Parameter(  
torch.zeros(1, num\_patches + 1, embed\_dim)  
)  
  
\# Transformer 层  
self.blocks = nn.ModuleList(\[  
TransformerBlock(embed\_dim, num\_heads)  
for \_ in range(depth)  
\])  
  
\# 最后的 LayerNorm 和分类头  
self.norm = nn.LayerNorm(embed\_dim)  
self.head = nn.Linear(embed\_dim, num\_classes)  
  
def forward(self, x: torch.Tensor) -> torch.Tensor:  
batch\_size = x.shape\[0\]  
  
\# Patch 嵌入  
x = self.patch\_embed(x) # (batch\_size, num\_patches, embed\_dim)  
  
\# 加上 class token  
cls\_tokens = self.cls\_token.expand(batch\_size, -1, -1)  
x = torch.cat((cls\_tokens, x), dim=1) # (batch\_size, 1+num\_patches, embed\_dim)  
  
\# 加上位置编码  
x = x + self.pos\_embed  
  
\# 通过 Transformer 层  
for block in self.blocks:  
x = block(x)  
  
\# 只用 class token 做分类  
x = self.norm(x)  
cls\_output = x\[:, 0\] # 取第一个位置（class token）  
x = self.head(cls\_output)  
  
return x  
  
\# 创建 ViT 模型并测试  
vit = SimpleViT(  
img\_size=32, # 用小一点的图像方便测试  
patch\_size=8, # 8×8 的 patch  
embed\_dim=128,  
depth=4,  
num\_heads=4,  
num\_classes=10  
)  
print("简化版 ViT 结构：")  
print(vit)  
  
test\_input = torch.randn(2, 3, 32, 32) # 2 张 32×32 的图  
test\_output = vit(test\_input)  
print(f"\\n输入形状: {test\_input.shape}")  
print(f"输出形状: {test\_output.shape}")
```

### CNN vs ViT 对比

两种架构各有优劣，选择哪一个取决于你的具体场景。

| 特性 | CNN | ViT |
| --- | --- | --- |
| 归纳偏置 | 强（局部性、平移不变性） | 弱（从数据中学习） |
| 数据需求 | 小数据下也能工作 | 需要大量数据才能发挥优势 |
| 计算效率 | 通常更快，硬件优化成熟 | 参数量大，计算密集 |
| 可解释性 | 容易可视化卷积核 | 注意力图可解释，但整体较难 |
| 长程依赖 | 需要多层才能建立 | 天然支持全局感受野 |
| 适用场景 | 数据量有限、实时性要求高 | 大数据集、复杂视觉任务 |

> 经验法则：数据量少时用 CNN（ResNet、EfficientNet），数据量百万级以上可以考虑 ViT。现在还有混合架构（如 ConvNeXt），结合了两者的优点。

---

## 目标检测

图像分类是回答"这张图里有什么"，目标检测要回答"这张图里有什么，它们分别在哪里"。

目标检测的输出是一系列边界框（Bounding Box），每个框对应一个物体类别。

### 两阶段检测：R-CNN 系列

两阶段检测的思路是：先找出可能有物体的区域，再对这些区域分类。

第一阶段：生成候选区域（Region Proposal），可能是几百个"这里可能有东西"的框。

第二阶段：对每个候选区域提取特征，判断是什么物体，同时调整框的位置。

R-CNN 系列的演进：

| 模型 | 核心改进 | 速度 |
| --- | --- | --- |
| R-CNN | 用 CNN 替代传统特征 | 慢（一张图几十秒） |
| Fast R-CNN | ROI Pooling，共享特征计算 | 中（一张图几秒） |
| Faster R-CNN | RPN（Region Proposal Network） | 快（一张图几百毫秒） |
| Mask R-CNN | 加分割分支 | 中 |

### 单阶段检测：YOLO 系列

单阶段检测的思路是：直接在图像上密集预测，不单独生成候选区域。

YOLO（You Only Look Once）是单阶段检测的代表，速度极快，适合实时应用。

YOLO 把图像分成 S×S 的网格，每个网格负责预测中心点落在这个格子里的物体。

每个网格预测：B 个边界框（含置信度）、C 个类别的概率。

### 端到端检测：DETR

DETR（DEtection TRansformer）是 Facebook AI 提出的新范式，用 Transformer 把目标检测变成一个直接的序列预测问题。

它的最大特点是：不需要非极大值抑制（NMS），不需要锚框（Anchor），输出就是最终结果。

### 实战：YOLOv11 目标检测

现在我们用 Ultralytics YOLOv11 来做一个完整的目标检测示例。

## 实例

\# ============================================  
\# YOLOv11 目标检测实战  
\# 包含加载模型、检测、可视化  
\# ============================================  
  
import torch  
import numpy as np  
from PIL import Image, ImageDraw, ImageFont  
import matplotlib.pyplot as plt  
  
class SimpleYOLO:  
"""简化的 YOLO 演示（使用预训练模型）"""  
  
def \_\_init\_\_(self, model\_name: str = "yolo11n.pt"):  
"""  
初始化 YOLO 模型  
  
参数:  
model\_name: 模型名称，yolo11n.pt（nano，小）、yolo11s.pt（small）等  
"""  
self.model\_name = model\_name  
self.model = None  
\# COCO 数据集的 80 个类别  
self.coco\_names = \[  
'person', 'bicycle', 'car', 'motorcycle', 'airplane', 'bus',  
'train', 'truck', 'boat', 'traffic light', 'fire hydrant',  
'stop sign', 'parking meter', 'bench', 'bird', 'cat', 'dog',  
'horse', 'sheep', 'cow', 'elephant', 'bear', 'zebra', 'giraffe',  
'backpack', 'umbrella', 'handbag', 'tie', 'suitcase', 'frisbee',  
'skis', 'snowboard', 'sports ball', 'kite', 'baseball bat',  
'baseball glove', 'skateboard', 'surfboard', 'tennis racket',  
'bottle', 'wine glass', 'cup', 'fork', 'knife', 'spoon', 'bowl',  
'banana', 'apple', 'sandwich', 'orange', 'broccoli', 'carrot',  
'hot dog', 'pizza', 'donut', 'cake', 'chair', 'couch',  
'potted plant', 'bed', 'dining table', 'toilet', 'tv', 'laptop',  
'mouse', 'remote', 'keyboard', 'cell phone', 'microwave', 'oven',  
'toaster', 'sink', 'refrigerator', 'book', 'clock', 'vase',  
'scissors', 'teddy bear', 'hair drier', 'toothbrush'  
\]  
\# 每个类别的颜色  
self.colors = np.random.randint(0, 255, (80, 3))  
  
def load\_model(self):  
"""加载预训练模型（这里用模拟实现演示概念）"""  
print(f"正在加载模型 {self.model\_name}...")  
print("提示：实际使用时请安装 ultralytics 并调用:")  
print(" from ultralytics import YOLO")  
print(" model = YOLO('yolo11n.pt')")  
self.model = "pretrained\_model\_loaded"  
print("模型加载完成！")  
return self  
  
def predict\_dummy(self, image: np.ndarray) -> list:  
"""  
模拟检测结果（用于演示）  
实际使用时调用 model(image)  
  
返回:  
检测结果列表，每个元素是一个字典:  
{'bbox': \[x1, y1, x2, y2\], 'class\_id': int, 'score': float, 'class\_name': str}  
"""  
\# 这里我们模拟检测到了几个物体  
h, w = image.shape\[:2\]  
dummy\_results = \[  
{'bbox': \[w\*0.1, h\*0.2, w\*0.3, h\*0.6\], 'class\_id': 0, 'score': 0.92, 'class\_name': 'person'},  
{'bbox': \[w\*0.35, h\*0.4, w\*0.7, h\*0.8\], 'class\_id': 2, 'score': 0.85, 'class\_name': 'car'},  
{'bbox': \[w\*0.75, h\*0.3, w\*0.9, h\*0.55\], 'class\_id': 16, 'score': 0.78, 'class\_name': 'dog'},  
\]  
return dummy\_results  
  
def visualize(self, image: np.ndarray, results: list) -> np.ndarray:  
"""  
在图像上绘制检测框和标签  
  
参数:  
image: 原始图像 (H, W, 3)  
results: 检测结果列表  
  
返回:  
标注后的图像  
"""  
if isinstance(image, np.ndarray):  
image = Image.fromarray(image)  
  
draw = ImageDraw.Draw(image)  
  
for result in results:  
bbox = result\['bbox'\]  
class\_id = result\['class\_id'\]  
score = result\['score'\]  
class\_name = result\['class\_name'\]  
  
color = tuple(self.colors\[class\_id % 80\].tolist())  
  
\# 画框  
draw.rectangle(bbox, outline=color, width=3)  
  
\# 标签背景  
label = f"{class\_name} {score:.2f}"  
try:  
font = ImageFont.truetype("arial.ttf", 16)  
except:  
font = ImageFont.load\_default()  
  
\# 计算文字大小  
bbox\_label = draw.textbbox((0, 0), label, font=font)  
text\_w = bbox\_label\[2\] - bbox\_label\[0\]  
text\_h = bbox\_label\[3\] - bbox\_label\[1\]  
  
\# 画标签背景  
label\_bg = \[bbox\[0\], bbox\[1\]-text\_h-5, bbox\[0\]+text\_w+5, bbox\[1\]\]  
draw.rectangle(label\_bg, fill=color)  
  
\# 画文字  
draw.text((bbox\[0\]+2, bbox\[1\]-text\_h-3), label, fill=(255,255,255), font=font)  
  
return np.array(image)  
  
\# 创建模拟图像（用于演示）  
def create\_test\_image() -> np.ndarray:  
"""创建一张测试图像"""  
h, w = 480, 640  
image = np.zeros((h, w, 3), dtype=np.uint8)  
\# 渐变背景  
image\[:,:, 0\] = np.linspace(100, 200, w)\[np.newaxis,:\]  
image\[:,:, 1\] = np.linspace(150, 100, h)\[:, np.newaxis\]  
image\[:,:, 2\] = 180  
return image  
  
\# 演示 YOLO 检测流程  
print("=" \* 60)  
print("RUNOOB YOLOv11 目标检测演示")  
print("=" \* 60)  
  
\# 初始化  
detector = SimpleYOLO().load\_model()  
  
\# 创建测试图像  
test\_image = create\_test\_image()  
print(f"\\n输入图像尺寸: {test\_image.shape}")  
  
\# 模拟检测  
results = detector.predict\_dummy(test\_image)  
print(f"\\n检测到 {len(results)} 个物体：")  
for i, r in enumerate(results, 1):  
print(f" {i}. {r\['class\_name'\]} (置信度: {r\['score'\]:.2f})")  
  
\# 可视化  
visualized = detector.visualize(test\_image, results)  
print(f"\\n可视化完成，输出图像尺寸: {visualized.shape}")  
  
\# ============================================  
\# 实际使用 YOLOv11 的代码（需要安装 ultralytics）  
\# ============================================  
print("\\n" + "=" \* 60)  
print("实际使用 YOLOv11 的代码示例：")  
print("=" \* 60)  
print("""  
\# 安装：pip install ultralytics  
  
from ultralytics import YOLO  
  
\# 1. 加载模型  
model = YOLO('yolo11n.pt') # 也可以用 'yolo11s.pt', 'yolo11m.pt' 等  
  
\# 2. 预测  
results = model('test\_image.jpg') # 可以是图片路径、视频、0（摄像头）  
  
\# 3. 处理结果  
for result in results:  
\# 检测框  
boxes = result.boxes  
\# 分割掩码（如果用分割模型）  
masks = result.masks  
\# 显示结果  
result.show()  
\# 保存结果  
result.save('result.jpg')  
  
\# 4. 训练（可选）  
\# model.train(data='coco128.yaml', epochs=100, imgsz=640)  
""")

---

## 图像分割

目标检测用矩形框标出物体，图像分割要精确到像素级别，标出每个像素属于哪个物体。

### 语义分割 vs 实例分割

图像分割有两大分支：语义分割和实例分割。

| 任务 | 目标 | 示例 | 代表模型 |
| --- | --- | --- | --- |
| 语义分割 | 给每个像素分类（不管实例） | 把所有人标成红色，所有车标成蓝色 | FCN、U-Net、DeepLab |
| 实例分割 | 给每个像素分类，且区分不同实例 | 把人1标成红色，人2标成橙色 | Mask R-CNN、YOLOv8-Seg |
| 全景分割 | 语义+实例，统一表示 | 同时标注所有"东西"和"背景" | Panoptic FPN、MaskFormer |

一个经典例子：假设有两个人和两条狗。

语义分割说：这里有"人"和"狗"（只分类别，不计数）。

实例分割说：这里有"人1"、"人2"、"狗1"、"狗2"（每个单独的个体都有区别）。

### Segment Anything Model（SAM）

2023 年 Meta AI 发布 SAM（Segment Anything Model），它把分割变成了一个提示词驱动的交互任务。

SAM 的输入可以是：一个点、一个框、一段文字、或者粗略的掩膜。

SAM 的输出是：对应物体的精确分割掩膜。

SAM 的设计理念是：可提示、可泛化、零样本。

## 实例

\# ============================================  
\# SAM 分割概念演示  
\# 展示如何用点、框等提示进行分割  
\# ============================================  
  
class SimpleSAM:  
"""简化版 SAM 概念演示"""  
  
def \_\_init\_\_(self):  
self.image\_embedding = None  
print("SAM 概念演示初始化")  
  
def set\_image(self, image: np.ndarray):  
"""编码图像（只做一次）"""  
print("正在编码图像为 embedding...")  
self.image\_embedding = "image\_encoded"  
return self  
  
def predict\_from\_point(self, point: tuple, point\_label: int = 1):  
"""  
从一个点进行分割  
  
参数:  
point: (x, y) 坐标  
point\_label: 1 表示前景，0 表示背景  
"""  
print(f"从点 {point} 进行分割（{'前景' if point\_label == 1 else '背景'}）")  
return "mask\_from\_point"  
  
def predict\_from\_box(self, box: tuple):  
"""从一个边界框进行分割"""  
x1, y1, x2, y2 = box  
print(f"从框 \[{x1}, {y1}, {x2}, {y2}\] 进行分割")  
return "mask\_from\_box"  
  
def predict\_from\_points(self, points: list, labels: list):  
"""从多个点进行分割"""  
print(f"从 {len(points)} 个点进行分割")  
return "mask\_from\_points"  
  
\# 演示 SAM 使用流程  
print("=" \* 60)  
print("RUNOOB SAM 分割概念演示")  
print("=" \* 60)  
  
sam = SimpleSAM()  
  
\# 1. 设置图像（编码一次）  
test\_img = create\_test\_image()  
sam.set\_image(test\_img)  
  
\# 2. 用不同提示进行分割  
print()  
mask1 = sam.predict\_from\_point((100, 200), point\_label=1)  
  
print()  
mask2 = sam.predict\_from\_box((50, 50, 200, 300))  
  
print()  
mask3 = sam.predict\_from\_points(\[(100, 100), (150, 150)\], \[1, 1\])  
  
\# ============================================  
\# 实际使用 SAM 的代码示例  
\# ============================================  
print("\\n" + "=" \* 60)  
print("实际使用 SAM 的代码示例：")  
print("=" \* 60)  
print("""  
\# 安装：pip install segment-anything torch torchvision opencv-python  
  
import cv2  
import numpy as np  
from segment\_anything import sam\_model\_registry, SamPredictor  
  
\# 1. 加载模型  
\# 下载 checkpoint: https://dl.fbaipublicfiles.com/segment\_anything/sam\_vit\_h\_4b8939.pth  
sam = sam\_model\_registry\["vit\_h"\](checkpoint="sam\_vit\_h\_4b8939.pth")  
sam.to(device="cuda")  
predictor = SamPredictor(sam)  
  
\# 2. 设置图像  
image = cv2.imread("test\_image.jpg")  
image = cv2.cvtColor(image, cv2.COLOR\_BGR2RGB)  
predictor.set\_image(image)  
  
\# 3. 用点提示分割  
input\_point = np.array(\[\[300, 250\]\]) # 提示点坐标  
input\_label = np.array(\[1\]) # 1 = 前景, 0 = 背景  
  
masks, scores, logits = predictor.predict(  
point\_coords=input\_point,  
point\_labels=input\_label,  
multimask\_output=True  
)  
  
\# 4. 可视化结果  
for i, mask in enumerate(masks):  
color = np.array(\[30, 144, 255\])  
h, w = mask.shape  
mask\_image = mask.reshape(h, w, 1) \* color.reshape(1, 1, -1)  
cv2.imwrite(f"mask\_{i}.jpg", mask\_image)  
""")

### 分割在工业中的应用

图像分割是很多工业应用的核心技术：

| 领域 | 应用场景 | 任务类型 |
| --- | --- | --- |
| 医学影像 | 肿瘤分割、器官测量 | 语义分割 |
| 自动驾驶 | 可行驶区域、车道线识别 | 语义分割 |
| 遥感影像 | 土地覆盖分类、建筑提取 | 语义分割 |
| 智能制造 | 缺陷检测、零件测量 | 实例分割 |
| 视频编辑 | 人物抠图、背景替换 | 实例分割 |

---

## 扩散模型（Diffusion Models）

扩散模型是 AI 图像生成的核心技术，Stable Diffusion、DALL-E 3、Midjourney 都基于它。

### 前向扩散过程（加噪）

前向扩散是一个简单的过程：逐步向图像添加高斯噪声，直到变成纯噪声。

这是一个确定的过程，每一步只加一点点噪声，我们可以用数学公式精确计算任意时刻的噪声量。

## 实例

\# ============================================  
\# 演示扩散模型的前向过程（加噪）  
\# ============================================  
  
import numpy as np  
import matplotlib.pyplot as plt  
  
class ForwardDiffusion:  
"""前向扩散过程演示"""  
  
def \_\_init\_\_(self, num\_timesteps: int = 1000):  
self.num\_timesteps = num\_timesteps  
  
\# 定义 beta 调度（从 beta\_start 到 beta\_end 线性增长）  
self.beta\_start = 0.0001  
self.beta\_end = 0.02  
self.betas = np.linspace(self.beta\_start, self.beta\_end, num\_timesteps)  
  
\# 计算 alpha 和 alpha\_bar  
self.alphas = 1.0 - self.betas  
self.alphas\_bar = np.cumprod(self.alphas)  
  
def q\_sample(self, x0: np.ndarray, t: int) -> tuple:  
"""  
从 x0 直接计算 t 时刻的 xt  
  
参数:  
x0: 原始图像 (H, W, 3)  
t: 时间步（0 <= t < num\_timesteps）  
  
返回:  
xt: t 时刻的图像  
noise: 添加的噪声  
"""  
sqrt\_alpha\_bar\_t = np.sqrt(self.alphas\_bar\[t\])  
sqrt\_one\_minus\_alpha\_bar\_t = np.sqrt(1 - self.alphas\_bar\[t\])  
  
\# 生成相同形状的随机噪声  
noise = np.random.randn(\*x0.shape)  
  
\# 一步到位计算 xt  
xt = sqrt\_alpha\_bar\_t \* x0 + sqrt\_one\_minus\_alpha\_bar\_t \* noise  
  
return xt, noise  
  
\# 创建一个简单的测试图像：中间一个亮圈  
def create\_diffusion\_test\_image():  
h, w = 64, 64  
x = np.linspace(-1, 1, w)  
y = np.linspace(-1, 1, h)  
xx, yy = np.meshgrid(x, y)  
r = np.sqrt(xx\*\*2 + yy\*\*2)  
image = np.exp(-r\*\*2 / 0.1) # 高斯分布的亮圈  
image = (image - image.min()) / (image.max() - image.min()) # 归一化到 \[0, 1\]  
image = np.stack(\[image, image\*0.8, image\*0.5\], axis=-1) # 变成 3 通道  
return image  
  
print("=" \* 60)  
print("RUNOOB 前向扩散过程演示")  
print("=" \* 60)  
  
\# 初始化扩散过程  
diffusion = ForwardDiffusion(num\_timesteps=1000)  
  
\# 创建测试图像  
x0 = create\_diffusion\_test\_image()  
print(f"原始图像形状: {x0.shape}")  
  
\# 展示不同时间步的加噪结果  
timesteps\_to\_show = \[0, 100, 200, 300, 500, 1000\]  
  
print("\\n不同时间步的加噪程度：")  
for t in timesteps\_to\_show:  
if t < 1000:  
xt, noise = diffusion.q\_sample(x0, t)  
alpha\_bar = diffusion.alphas\_bar\[t\]  
print(f" t={t:4d}: alpha\_bar={alpha\_bar:.4f}, 信噪比={alpha\_bar/(1-alpha\_bar):.4f}")  
else:  
print(f" t={t:4d}: 纯噪声")  
  
print("\\n前向扩散的核心公式：")  
print(" q(x\_t | x\_0) = N(x\_t; sqrt(alpha\_bar\_t) \* x0, (1 - alpha\_bar\_t) \* I)")  
print("\\n这个公式让我们可以从 x0 一步计算出任意 t 时刻的 x\_t，不需要逐步迭代！")

### 反向去噪过程

反向过程是前向过程的逆：从纯噪声开始，逐步预测并去除噪声，最终得到图像。

这是我们训练模型要学的：给定 xt 和 t，预测添加的噪声是什么。

训练目标：让模型预测的噪声和真实噪声的均方误差最小。

### U-Net 架构

扩散模型的核心是一个 U-Net 网络，它的形状像字母"U"：左边下采样压缩，右边上采样恢复。

U-Net 的关键是跳跃连接（Skip Connection），它把下采样时的特征直接传给上采样时的对应层，保留细节信息。

### DDPM vs DDIM 采样

训练好模型后，我们需要从噪声中采样生成图像，不同的采样器速度和质量不同。

| 采样器 | 全称 | 步数 | 特点 |
| --- | --- | --- | --- |
| DDPM | Denoising Diffusion Probabilistic Models | 1000 | 原始方法，质量好但慢 |
| DDIM | Denoising Diffusion Implicit Models | 50-100 | 确定性采样，速度快 |
| LMS | Linear Multistep Method | 30-50 | 速度快，质量好 |
| DPM-Solver | DPM-Solver | 10-20 | 超快速，几步就能出好效果 |

> 现在的 Stable Diffusion 默认用 DPM-Solver++，通常 20-30 步就能得到高质量结果，比最早的 DDPM 快几十倍。

### Stable Diffusion 架构

Stable Diffusion 不是直接在像素空间工作，而是在隐空间（Latent Space）工作，这让它更快更高效。

完整的 Stable Diffusion 包含三个部分：

1\. VAE（Variational Autoencoder）：把图像压缩成隐向量，或者把隐向量还原成图像。

2\. UNet：核心去噪网络，接收隐向量和时间步、提示词 embedding，预测噪声。

3\. Text Encoder（CLIP）：把文字提示转换成 embedding，告诉模型生成什么。

## 实例

\# ============================================  
\# Stable Diffusion 概念演示  
\# 展示完整的生成流程  
\# ============================================  
  
class SimpleStableDiffusion:  
"""简化版 Stable Diffusion 概念演示"""  
  
def \_\_init\_\_(self):  
print("Stable Diffusion 概念演示初始化")  
  
def encode\_text(self, prompt: str):  
"""编码文字提示"""  
print(f"编码提示词: '{prompt}'")  
return "text\_embedding"  
  
def encode\_image(self, image: np.ndarray):  
"""VAE 编码图像到隐空间"""  
print("编码图像到隐空间（VAE 编码器）")  
return "latent\_code"  
  
def decode\_latent(self, latent: str):  
"""VAE 从隐空间解码图像"""  
print("从隐空间解码图像（VAE 解码器）")  
return "decoded\_image"  
  
def denoise\_step(self, latent: str, text\_embedding: str, t: int):  
"""一步去噪"""  
print(f" 去噪步骤 t={t}")  
return "denoised\_latent"  
  
def generate(self, prompt: str, num\_inference\_steps: int = 50):  
"""完整生成流程"""  
print("=" \* 60)  
print(f"开始生成: '{prompt}'")  
print("=" \* 60)  
  
\# 1. 编码文字  
text\_emb = self.encode\_text(prompt)  
  
\# 2. 从随机噪声开始  
print("\\n初始化随机噪声...")  
latent = "random\_noise"  
  
\# 3. 逐步去噪  
print(f"\\n开始去噪循环（{num\_inference\_steps} 步）：")  
for t in reversed(range(num\_inference\_steps)):  
latent = self.denoise\_step(latent, text\_emb, t)  
  
\# 4. 解码得到图像  
print("\\n解码隐向量到图像...")  
image = self.decode\_latent(latent)  
  
print("\\n生成完成！")  
return image  
  
\# 演示生成流程  
sd = SimpleStableDiffusion()  
image = sd.generate(  
prompt="一只可爱的橘猫在花园里",  
num\_inference\_steps=20  
)  
  
\# ============================================  
\# 实际使用 Stable Diffusion 的代码示例  
\# ============================================  
print("\\n" + "=" \* 60)  
print("实际使用 Stable Diffusion 的代码示例：")  
print("=" \* 60)  
print("""  
\# 安装：pip install diffusers transformers accelerate torch  
  
from diffusers import StableDiffusionPipeline  
import torch  
  
\# 1. 加载模型  
model\_id = "runwayml/stable-diffusion-v1-5"  
pipe = StableDiffusionPipeline.from\_pretrained(  
model\_id,  
torch\_dtype=torch.float16  
)  
pipe = pipe.to("cuda")  
  
\# 2. 文生图  
prompt = "a cute orange cat in a garden"  
image = pipe(prompt).images\[0\]  
  
\# 3. 保存结果  
image.save("cat\_garden.png")  
  
\# 4. 图生图  
from diffusers import StableDiffusionImg2ImgPipeline  
from PIL import Image  
  
img\_pipe = StableDiffusionImg2ImgPipeline.from\_pretrained(  
model\_id,  
torch\_dtype=torch.float16  
).to("cuda")  
  
init\_image = Image.open("input.jpg").convert("RGB")  
init\_image = init\_image.resize((512, 512))  
  
image = img\_pipe(  
prompt="make it look like a painting",  
image=init\_image,  
strength=0.75 # 变化程度  
).images\[0\]  
image.save("painted.png")  
  
\# 5. Inpainting（局部修改）  
from diffusers import StableDiffusionInpaintPipeline  
  
inpaint\_pipe = StableDiffusionInpaintPipeline.from\_pretrained(  
model\_id,  
torch\_dtype=torch.float16  
).to("cuda")  
  
image = inpaint\_pipe(  
prompt="put a hat on the cat",  
image=init\_image,  
mask\_image=mask\_image # 掩码图，白色区域是要修改的部分  
).images\[0\]  
""")

---

## CLIP：多模态对齐基础

CLIP（Contrastive Language-Image Pre-training）是 OpenAI 2021 年发表的工作，它第一次把文字和图像真正对齐到了同一个向量空间。

### 对比学习原理

CLIP 的训练方式是对比学习：让正确的图文对相似度高，错误的图文对相似度低。

训练时，一个 batch 里有 N 张图和 N 段文字（一一对应），形成 N×N 个可能的图文对。

对角线是匹配的正样本，其他都是不匹配的负样本。

训练目标就是：让正样本的相似度尽可能大，负样本的相似度尽可能小。

### 图文对齐训练

CLIP 包含两个编码器：

- 1\. Image Encoder：把图像编码成向量（可以是 ResNet 或 ViT）。
- 2\. Text Encoder：把文字编码成向量（Transformer）。

两个向量投影到同一个空间后，通过余弦相似度计算图文的匹配程度。

## 实例

\# ============================================  
\# CLIP 概念演示：对比学习和图文对齐  
\# ============================================  
  
import torch  
import torch.nn.functional as F  
  
class SimpleCLIP:  
"""简化版 CLIP 概念演示"""  
  
def \_\_init\_\_(self, embed\_dim: int = 512):  
self.embed\_dim = embed\_dim  
\# 简单模拟：不真实编码，只是随机投影  
print("CLIP 概念演示初始化")  
print(f"Embedding 维度: {embed\_dim}")  
  
def encode\_image(self, image: str) -> torch.Tensor:  
"""模拟图像编码"""  
\# 实际中这里是 Vision Transformer 或 ResNet  
return torch.randn(1, self.embed\_dim)  
  
def encode\_text(self, text: str) -> torch.Tensor:  
"""模拟文字编码"""  
\# 实际中这里是 Text Transformer  
return torch.randn(1, self.embed\_dim)  
  
def compute\_similarity(self, image\_emb: torch.Tensor, text\_emb: torch.Tensor) -> float:  
"""计算余弦相似度"""  
image\_emb = F.normalize(image\_emb, dim=-1)  
text\_emb = F.normalize(text\_emb, dim=-1)  
return (image\_emb @ text\_emb.T).item()  
  
\# 演示 CLIP 推理流程  
print("=" \* 60)  
print("RUNOOB CLIP 零样本分类演示")  
print("=" \* 60)  
  
clip = SimpleCLIP(embed\_dim=512)  
  
\# 假设有一张图  
print("\\n1. 编码图像...")  
image\_emb = clip.encode\_image("一张猫的照片")  
  
\# 假设有几个候选类别  
candidates = \["一张狗的照片", "一张猫的照片", "一辆汽车", "一个人"\]  
print(f"\\n2. 编码候选文本: {candidates}")  
  
\# 计算相似度  
print("\\n3. 计算图文相似度：")  
for text in candidates:  
text\_emb = clip.encode\_text(text)  
sim = clip.compute\_similarity(image\_emb, text\_emb)  
print(f" '{text}' → 相似度: {sim:.3f}")  
  
print("\\n（实际中，'一张猫的照片' 会有最高的相似度）")  
  
\# ============================================  
\# 实际使用 CLIP 的代码示例  
\# ============================================  
print("\\n" + "=" \* 60)  
print("实际使用 CLIP 的代码示例：")  
print("=" \* 60)  
print("""  
\# 安装：pip install clip torch torchvision  
  
import clip  
import torch  
from PIL import Image  
  
\# 1. 加载模型  
device = "cuda" if torch.cuda.is\_available() else "cpu"  
model, preprocess = clip.load("ViT-B/32", device=device)  
  
\# 2. 准备图像  
image = preprocess(Image.open("cat.jpg")).unsqueeze(0).to(device)  
  
\# 3. 准备文本  
text = clip.tokenize(\[  
"a photo of a dog",  
"a photo of a cat",  
"a photo of a car",  
"a photo of a person"  
\]).to(device)  
  
\# 4. 推理  
with torch.no\_grad():  
image\_features = model.encode\_image(image)  
text\_features = model.encode\_text(text)  
  
logits\_per\_image, logits\_per\_text = model(image, text)  
probs = logits\_per\_image.softmax(dim=-1).cpu().numpy()  
  
\# 5. 输出结果  
print("预测概率：")  
for i, p in enumerate(probs\[0\]):  
print(f" {i+1}. {p:.2%}")  
""")

### Zero-Shot 分类能力

CLIP 最令人兴奋的能力是 Zero-Shot 分类：不需要在目标数据集上训练，直接就能分类。

传统 ImageNet 训练的模型，只能分 ImageNet 的 1000 类，要加新类得重新训练。

CLIP 可以用任意文字描述类别，比如："一张猫的照片"、"一只坐在草地上的橘猫"、"一张猫的素描画"。

> CLIP 的意义不仅在于零样本分类，更在于它建立了一个通用的"视觉-语言"接口，后续的很多模型（如 Stable Diffusion、视觉语言模型）都建立在它的基础上。

---

## 视觉语言模型（VLM）

视觉语言模型（Vision-Language Model，VLM）把图像理解和语言生成结合起来，让模型能够"看图说话"。

### LLaVA 架构

LLaVA（Large Language and Vision Assistant）是一个开源的 VLM，它的设计简洁清晰，很适合入门学习。

LLaVA 的架构分成三部分：

- 1\. Vision Encoder：CLIP 的视觉编码器，把图像转换成 embedding。
- 2\. Projection Layer：把视觉 embedding 投影成和 LLM 输入一样的维度。
- 3\. LLM：一个大语言模型（如 Vicuna、LLaMA），接收拼接后的图文输入，生成回答。

训练分两步：

- 1\. 预训练阶段：只训练 Projection Layer，用大量图文对训练对齐。
- 2\. 指令微调阶段：用视觉指令数据集（如 LLaVA-Instruct）训练整个模型。

### 视觉 Encoder 与 LLM 的连接

连接视觉和语言的关键在于：把图像表示成语言模型能理解的 token 序列。

有几种常见的连接方式：

| 方式 | 特点 | 代表模型 |
| --- | --- | --- |
| 特殊 token | 用一个 \[IMG\] token 代表整张图 | 早期模型 |
| 序列拼接 | 把图像 patch embedding 直接拼在文本前 | LLaVA、GPT-4V |
| 交叉注意力 | 在 LLM 中加入对视觉特征的交叉注意力 | BLIP-2、Flamingo |

## 实例

\# ============================================  
\# 视觉语言模型（VLM）概念演示  
\# 展示如何拼接视觉和语言输入  
\# ============================================  
  
class SimpleVLM:  
"""简化版视觉语言模型"""  
  
def \_\_init\_\_(self, vocab\_size: int = 50000, hidden\_size: int = 768):  
self.vocab\_size = vocab\_size  
self.hidden\_size = hidden\_size  
print("简化版 VLM 初始化")  
  
def encode\_vision(self, image: str) -> torch.Tensor:  
"""编码图像为序列"""  
\# 假设有 256 个视觉 token  
return torch.randn(1, 256, self.hidden\_size)  
  
def encode\_text(self, text: str) -> torch.Tensor:  
"""编码文本为序列"""  
\# 假设文本有 16 个 token  
return torch.randn(1, 16, self.hidden\_size)  
  
def generate(self, image: str, question: str) -> str:  
"""图文拼接生成回答"""  
print("=" \* 60)  
print(f"问题: {question}")  
print("=" \* 60)  
  
\# 1. 编码图像和文本  
print("\\n1. 编码图像...")  
vision\_emb = self.encode\_vision(image)  
  
print("2. 编码文本...")  
text\_emb = self.encode\_text(question)  
  
\# 3. 拼接：\[视觉序列\] + \[文本序列\]  
print("\\n3. 拼接视觉和文本序列...")  
print(f" 视觉 embedding 形状: {vision\_emb.shape}")  
print(f" 文本 embedding 形状: {text\_emb.shape}")  
combined = torch.cat(\[vision\_emb, text\_emb\], dim=1)  
print(f" 拼接后形状: {combined.shape}")  
  
\# 4. LLM 生成回答  
print("\\n4. LLM 生成回答...")  
  
return "这是一个可爱的橘猫，它看起来很开心。"  
  
\# 演示 VLM 问答  
vlm = SimpleVLM()  
answer = vlm.generate(  
image="一张橘猫的照片",  
question="这张图里有什么？请描述一下。"  
)  
  
print(f"\\n回答: {answer}")  
  
\# ============================================  
\# 实际使用 LLaVA 的代码示例  
\# ============================================  
print("\\n" + "=" \* 60)  
print("实际使用 LLaVA 的代码示例：")  
print("=" \* 60)  
print("""  
\# LLaVA 需要较多显存，推荐使用 transformers 或 llava 库  
  
from transformers import AutoProcessor, AutoModelForVisionAndLanguageGeneration  
import torch  
from PIL import Image  
  
\# 1. 加载模型和处理器  
model\_id = "llava-hf/llava-1.5-7b-hf"  
processor = AutoProcessor.from\_pretrained(model\_id)  
model = AutoModelForVisionAndLanguageGeneration.from\_pretrained(  
model\_id,  
torch\_dtype=torch.float16,  
device\_map="auto"  
)  
  
\# 2. 准备输入  
image = Image.open("cat.jpg")  
prompt = "USER: \<image>\\\\n这张图里有什么？请描述一下。 ASSISTANT:"  
  
inputs = processor(images=image, text=prompt, return\_tensors="pt").to("cuda")  
  
\# 3. 生成  
with torch.no\_grad():  
output = model.generate(\*\*inputs, max\_new\_tokens=500)  
answer = processor.decode(output\[0\], skip\_special\_tokens=True)  
  
print(answer)  
""")

---

## 实战：图像分类 + 目标检测完整项目

现在我们把前面学到的知识整合起来，做一个完整的实战项目。

## 实例

\# ============================================  
\# 实战项目：完整的视觉 AI 流水线  
\# 包含图像分类、目标检测、结果可视化  
\# ============================================  
  
import torch  
import torch.nn as nn  
import torch.nn.functional as F  
from torch.utils.data import Dataset, DataLoader  
import numpy as np  
from PIL import Image  
import json  
from pathlib import Path  
  
class VisionPipeline:  
"""  
完整的视觉 AI 流水线  
  
功能:  
1\. 图像分类  
2\. 目标检测  
3\. 结果汇总和可视化  
"""  
  
def \_\_init\_\_(self, device: str = "cpu"):  
self.device = device  
self.classifier = None  
self.detector = None  
print(f"RUNOOB Vision Pipeline 初始化（设备: {device}）")  
  
def build\_classifier(self, num\_classes: int = 10):  
"""构建图像分类器"""  
print("\\n正在构建图像分类器...")  
\# 使用前面定义的 SimpleCNN  
self.classifier = SimpleCNN(num\_classes=num\_classes).to(self.device)  
print("分类器构建完成！")  
return self  
  
def build\_detector(self):  
"""构建目标检测器"""  
print("\\n正在构建目标检测器...")  
self.detector = SimpleYOLO()  
print("检测器构建完成！")  
return self  
  
def load\_pretrained\_weights(self, classifier\_path: str = None):  
"""加载预训练权重"""  
if classifier\_path and self.classifier:  
print(f"\\n加载分类器权重: {classifier\_path}")  
\# 实际中: self.classifier.load\_state\_dict(torch.load(classifier\_path))  
return self  
  
def classify\_image(self, image: np.ndarray) -> tuple:  
"""  
图像分类  
  
返回:  
(pred\_class\_id, pred\_class\_name, confidence)  
"""  
if self.classifier is None:  
raise ValueError("请先构建分类器: build\_classifier()")  
  
\# 预处理  
if isinstance(image, np.ndarray):  
if image.ndim == 2: # 灰度图  
image = np.stack(\[image\]\*3, axis=-1)  
image = torch.from\_numpy(image).permute(2, 0, 1).float() / 255.0  
image = F.interpolate(image.unsqueeze(0), size=(32, 32), mode='bilinear')  
image = image.to(self.device)  
  
\# 推理  
self.classifier.eval()  
with torch.no\_grad():  
logits = self.classifier(image)  
probs = F.softmax(logits, dim=-1)  
confidence, pred\_idx = probs.max(dim=-1)  
  
\# 返回结果  
class\_names = \[  
'airplane', 'automobile', 'bird', 'cat', 'deer',  
'dog', 'frog', 'horse', 'ship', 'truck'  
\]  
pred\_class = class\_names\[pred\_idx.item()\]  
  
return pred\_idx.item(), pred\_class, confidence.item()  
  
def detect\_objects(self, image: np.ndarray) -> list:  
"""目标检测"""  
if self.detector is None:  
raise ValueError("请先构建检测器: build\_detector()")  
  
\# 使用模拟检测  
return self.detector.predict\_dummy(image)  
  
def process\_image(self, image: np.ndarray) -> dict:  
"""  
完整处理一张图像  
  
返回:  
包含所有结果的字典  
"""  
print("\\n" + "=" \* 60)  
print("开始处理图像")  
print("=" \* 60)  
  
results = {}  
  
\# 1. 图像分类  
if self.classifier is not None:  
print("\\n\[1/3\] 运行图像分类...")  
class\_id, class\_name, conf = self.classify\_image(image)  
results\['classification'\] = {  
'class\_id': class\_id,  
'class\_name': class\_name,  
'confidence': conf  
}  
print(f" → 分类结果: {class\_name} (置信度: {conf:.2%})")  
  
\# 2. 目标检测  
if self.detector is not None:  
print("\\n\[2/3\] 运行目标检测...")  
detections = self.detect\_objects(image)  
results\['detections'\] = detections  
print(f" → 检测到 {len(detections)} 个物体:")  
for det in detections:  
print(f" - {det\['class\_name'\]} ({det\['score'\]:.2f})")  
  
\# 3. 可视化  
print("\\n\[3/3\] 生成可视化...")  
if self.detector is not None:  
vis\_image = self.detector.visualize(image, detections)  
results\['visualization'\] = vis\_image  
print(" → 可视化完成")  
  
print("\\n" + "=" \* 60)  
print("图像处理完成！")  
print("=" \* 60)  
  
return results  
  
def save\_results(self, results: dict, output\_dir: str = "output"):  
"""保存结果到文件"""  
output\_path = Path(output\_dir)  
output\_path.mkdir(exist\_ok=True)  
  
\# 保存 JSON 结果  
json\_result = {  
'classification': results.get('classification'),  
'detections': \[  
{k: v for k, v in det.items() if k!= 'bbox'}  
for det in results.get('detections', \[\])  
\]  
}  
with open(output\_path / "results.json", "w") as f:  
json.dump(json\_result, f, indent=2, ensure\_ascii=False)  
  
\# 保存可视化图像  
if 'visualization' in results:  
vis = Image.fromarray(results\['visualization'\])  
vis.save(output\_path / "visualization.jpg")  
  
print(f"\\n结果已保存到: {output\_path}/")  
  
\# 完整的训练示例  
class SimpleDataset(Dataset):  
"""简单的模拟数据集"""  
  
def \_\_init\_\_(self, num\_samples: int = 100):  
self.num\_samples = num\_samples  
\# 生成假数据  
self.images = torch.randn(num\_samples, 3, 32, 32)  
self.labels = torch.randint(0, 10, (num\_samples,))  
  
def \_\_len\_\_(self):  
return self.num\_samples  
  
def \_\_getitem\_\_(self, idx):  
return self.images\[idx\], self.labels\[idx\]  
  
def train\_classifier(pipeline: VisionPipeline, num\_epochs: int = 5):  
"""训练分类器的完整流程"""  
print("\\n" + "=" \* 60)  
print("开始训练分类器")  
print("=" \* 60)  
  
\# 1. 准备数据  
print("\\n准备数据集...")  
train\_dataset = SimpleDataset(1000)  
val\_dataset = SimpleDataset(200)  
  
train\_loader = DataLoader(train\_dataset, batch\_size=32, shuffle=True)  
val\_loader = DataLoader(val\_dataset, batch\_size=32)  
  
print(f"训练集: {len(train\_dataset)} 样本")  
print(f"验证集: {len(val\_dataset)} 样本")  
  
\# 2. 优化器和损失函数  
optimizer = torch.optim.Adam(pipeline.classifier.parameters(), lr=1e-3)  
criterion = nn.CrossEntropyLoss()  
  
\# 3. 训练循环  
print("\\n开始训练...")  
for epoch in range(num\_epochs):  
pipeline.classifier.train()  
total\_loss = 0.0  
correct = 0  
total = 0  
  
for images, labels in train\_loader:  
images, labels = images.to(pipeline.device), labels.to(pipeline.device)  
  
optimizer.zero\_grad()  
outputs = pipeline.classifier(images)  
loss = criterion(outputs, labels)  
loss.backward()  
optimizer.step()  
  
total\_loss += loss.item()  
\_, predicted = outputs.max(1)  
total += labels.size(0)  
correct += predicted.eq(labels).sum().item()  
  
train\_acc = correct / total  
avg\_loss = total\_loss / len(train\_loader)  
  
\# 验证  
pipeline.classifier.eval()  
correct = 0  
total = 0  
with torch.no\_grad():  
for images, labels in val\_loader:  
images, labels = images.to(pipeline.device), labels.to(pipeline.device)  
outputs = pipeline.classifier(images)  
\_, predicted = outputs.max(1)  
total += labels.size(0)  
correct += predicted.eq(labels).sum().item()  
  
val\_acc = correct / total  
  
print(f"Epoch {epoch+1}/{num\_epochs} - "  
f"损失: {avg\_loss:.4f}, "  
f"训练准确率: {train\_acc:.2%}, "  
f"验证准确率: {val\_acc:.2%}")  
  
print("\\n训练完成！")  
  
\# ============================================  
\# 运行完整实战项目  
\# ============================================  
  
print("=" \* 60)  
print("RUNOOB 视觉 AI 完整实战项目")  
print("=" \* 60)  
  
\# 1. 创建流水线  
pipeline = VisionPipeline(device="cuda" if torch.cuda.is\_available() else "cpu")  
  
\# 2. 构建模型  
pipeline.build\_classifier(num\_classes=10)  
pipeline.build\_detector()  
  
\# 3. 训练分类器  
train\_classifier(pipeline, num\_epochs=3)  
  
\# 4. 处理图像  
test\_image = create\_test\_image()  
results = pipeline.process\_image(test\_image)  
  
\# 5. 保存结果  
pipeline.save\_results(results, output\_dir="runoob\_output")  
  
print("\\n" + "=" \* 60)  
print("项目运行完成！")  
print("=" \* 60)  
print("\\n下一步学习建议：")  
print(" 1. 尝试在真实数据集上训练（如 CIFAR-10）")  
print(" 2. 使用预训练模型进行微调")  
print(" 3. 尝试更复杂的模型架构")  
print(" 4. 部署到实际应用中")