---
title: 张量操作
tag:
  - PyTorch
  - 机器学习
category:
  - 工具学习
lang: zh-CN
copy_from: 'https://blog.csdn.net/xhyu61/article/details/132924767'
abbrlink: a9aec935
date: 2023-09-17 00:00:00
---

文章介绍了PyTorch中关于张量操作的几个重要函数，包括拼接函数`torch.cat()`，堆叠函数`torch.stack()`，均等切分函数`torch.chunk()`，长度切分函数`torch.split()`，索引选择函数`torch.index_select()`，掩码选择函数`torch.masked_select()`，形状变换函数`torch.reshape()`，维度交换函数`torch.transpose()`，转置函数`torch.t()`，压缩与扩展维度函数`torch.squeeze()`和`torch.unsqueeze()`。每个函数都附有详细的使用示例，帮助理解函数的操作。视频链接提供了更详细的讲解。

<!--more-->

参考视频：
[深度之眼官方账号 - 01-03-mp4-张量操作与线性回归](https://www.bilibili.com/video/BV1QB4y1v7br/?spm_id_from=333.788&vd_source=5e736c77221d69ad61b7490451a44145)

## torch.cat()
功能：将两个tensor按照给定的维度进行拼接。
```python
torch.cat(tensors,
          dim=0,
          out=None)
```
tensors：包含一坨tensor的list或tuple；
dim：指定拼接的维度；
out：输出到哪个tensor中。

```python
import numpy as np
import torch

t1 = torch.ones((4, 4))
t2 = torch.zeros((4, 3))
t3 = torch.cat((t1, t2), dim=1)
print(t3)
```
输出：
```python
tensor([[1., 1., 1., 1., 0., 0., 0.],
        [1., 1., 1., 1., 0., 0., 0.],
        [1., 1., 1., 1., 0., 0., 0.],
        [1., 1., 1., 1., 0., 0., 0.]])
```

## torch.stack()
功能：将多个tensor按照指定的维度进行堆叠（stack），创建一个新的张量。

```python
torch.stack(tensors,
            dim=0,
            out=None)
```

```python
import numpy as np
import torch

t1 = torch.ones((2, 2))
t2 = torch.zeros((2, 2))
t3 = torch.stack((t1, t2), dim=2)
print(t3)
```
输出：
```python
tensor([[[1., 0.],
         [1., 0.]],

        [[1., 0.],
         [1., 0.]]])
```
两个tensor只能在dim维度上不同，其他维度的大小必须相同。

## torch.chunk()
功能：将一个tensor在给定维度上进行均等切分，若不能均等切分，最后一份切片的大小小于其他切片。

```python
torch.chunk(input,
            chunks,
            dim=0)
```
input：被切分的tensor；
chunks：切分份数；
dim：在dim维度上进行切分

```python
import numpy as np
import torch

t1 = torch.ones((4, 2))
t_list = torch.chunk(t1, chunks=2, dim=0)
print(t_list)
```
输出：
```python
(tensor([[1., 1.],
        [1., 1.]]), tensor([[1., 1.],
        [1., 1.]]))
```

## torch.split()
功能：将tensor按照给定的长度进行切分。

```python
torch.split(tensor,
            split_size_or_sections,
            dim=0)
```
split_size_or_sections：如果是int，则切出的长度全为这个值；如果是列表，则按列表中的数作为尺寸来切。
dim：在dim维度上进行切分。

```python
import numpy as np
import torch

t1 = torch.ones((4, 2))
t_list = torch.split(t1, (1, 2, 1), dim=0)
print(t_list)
```
输出：
```python
(tensor([[1., 1.]]), tensor([[1., 1.],
        [1., 1.]]), tensor([[1., 1.]]))
```

## torch.index_select()
功能：在维度dim上，按index索引数据，返回依据index索引拼接的tensor。

```python
torch.index_select(input,
                   dim,
                   index,
                   out=None)
```
input：待搜索数据的tensor；
dim：在维度dim上的维度；
index：包含所有索引需求的tensor。

```python
import numpy as np
import torch

t1 = torch.tensor([[2, 3], [4, 5], [6, 7]])
t2 = torch.tensor([0, 2])
t_out = torch.index_select(t1, dim=0, index=t2)
print(t_out)
```
输出：
```python
tensor([[2, 3],
        [6, 7]])
```

## torch.masked_select()
功能：在维度dim上，按mask索引数据，返回依据mask索引拼接的tensor。

```python
torch.masked_select(input,
                    mask,
                    out=None)
```
input：待搜索数据的tensor；
mask：在维度dim上需要索引的位置为True，不需要索引的位置为False，形成一个全为布尔量的tensor。该tensor与input同形状。
```python
import numpy as np
import torch

t1 = torch.tensor([[2, 3], [4, 5], [6, 7]])
t2 = torch.tensor([[True, False],[True, True], [False, True]])
t_out = torch.masked_select(t1, mask=t2)
print(t_out)
```
输出
```python
tensor([2, 4, 5, 7])
```

## torch.reshape()
功能：变换张量形状。
（当张量在内存中连续时，reshape后的tensor将存储在原来的地址内）

```python
torch.reshape(input, 
              shape)
```
input：待变换形状的tensor；
shape：变换后的形状。（形状中至多一位可以填为-1，由于原tensor中的元素数量是固定的，-1的位置应该填写的数会自动算出）
```python
import numpy as np
import torch

t1 = torch.tensor([[2, 3, 4], [5, 6, 7]])
t2 = torch.reshape(t1, (3, 2))
print(t2)
```
输出：
```python
tensor([[2, 3],
        [4, 5],
        [6, 7]])
```

## torch.transpose()
功能：交换张量的两个维度。
```python
torch.transpose(input,
                dim0,
                dim1)
```
dim0、dim1：被交换的两个维度。

```python
import numpy as np
import torch

t = torch.tensor([[2, 3, 4], [5, 6, 7]])
t1 = torch.transpose(t, 0, 1)
print(t1)
```
输出：

```python
tensor([[2, 5],
        [3, 6],
        [4, 7]])
```
## torch.t()
功能：仅限于二维tensor的维度交换。
（二维tensor中，等价于`torch.transpose(input, 0, 1)`）
```python
torch.t(input)
```
input：待进行维度交换的tensor。

```python
import numpy as np
import torch

t = torch.tensor([[2, 3, 4], [5, 6, 7]])
t1 = torch.t(t)
print(t1)
```
输出：
```python
tensor([[2, 5],
        [3, 6],
        [4, 7]])
```

## torch.squeeze()
功能：压缩维度为1的轴。
```python
torch.squeeze(input,
              dim=None,
              out=None)
```
dim：指定维度进行压缩，这个维度的长度必须为1。

```python
import numpy as np
import torch

t = torch.tensor([[2], [3], [4], [5], [6]])
t1 = torch.squeeze(t)
print(t1)
```
输出：
```python
tensor([2, 3, 4, 5, 6])
```

## torch.unsqueeze()
功能：依据维度dim扩展维度。

```python
torch.unsqueeze(input,
                dim,
                out=None)
```
dim：指定维度dim进行扩展，不同于torch.squeeze()的默认为0，这里必须要指明dim的数值。
```python
import numpy as np
import torch

t = torch.tensor([2, 3, 4, 5, 6])
t1 = torch.unsqueeze(t, dim=0)
print(t1)
```
输出：
```python
tensor([[2, 3, 4, 5, 6]])
```