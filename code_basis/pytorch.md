# 示例
```python
import torch  
  
\# 设置数据类型和设备  
dtype = torch.float # 张量数据类型为浮点型  
device = torch.device("cpu") # 本次计算在 CPU 上进行  
  
\# 创建并打印两个随机张量 a 和 b  
a = torch.randn(2, 3, device=device, dtype=dtype) # 创建一个 2x3 的随机张量  
b = torch.randn(2, 3, device=device, dtype=dtype) # 创建另一个 2x3 的随机张量  
  
print("张量 a:")  
print(a)  
  
print("张量 b:")  
print(b)  
  
\# 逐元素相乘并输出结果  
print("a 和 b 的逐元素乘积:")  
print(a \* b)  
  
\# 输出张量 a 所有元素的总和  
print("张量 a 所有元素的总和:")  
print(a.sum())  
  
\# 输出张量 a 中第 2 行第 3 列的元素（注意索引从 0 开始）  
print("张量 a 第 2 行第 3 列的元素:")  
print(a\[1, 2\])  
  
\# 输出张量 a 中的最大值  
print("张量 a 中的最大值:")  
print(a.max())
```


**创建张量：**

- `torch.randn(2, 3)` 创建一个 2 行 3 列的张量，填充随机数（遵循正态分布）。
- `device=device` 和 `dtype=dtype` 分别指定了计算设备（CPU 或 GPU）和数据类型（浮点型）。

**张量操作：**

- `a * b` ：逐元素相乘。
- `a.sum()` ：计算张量 `a` 所有元素的和。
- `a[1, 2]` ：访问张量 `a` 第 2 行第 3 列的元素（注意索引从 0 开始）。
- `a.max()` ：获取张量 `a` 中的最大值。

输出：（每次运行时值会有所不同）

```
张量 a:
tensor([[-0.1460, -0.3490,  0.3705],
        [-1.1141,  0.7661,  1.0823]])

张量 b:
tensor([[ 0.6901, -0.9663,  0.3634],
        [-0.6538, -0.3728, -1.1323]])

a 和 b 的逐元素乘积:
tensor([[-0.1007,  0.3372,  0.1346],
        [ 0.7284, -0.2856, -1.2256]])

张量 a 所有元素的总和:
tensor(0.6097)

张量 a 第 2 行第 3 列的元素:
tensor(1.0823)

张量 a 中的最大值:
tensor(1.0823)
```