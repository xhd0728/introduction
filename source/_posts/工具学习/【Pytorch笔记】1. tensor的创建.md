---
title: tensor的创建
tag:
  - PyTorch
  - 机器学习
category:
  - 工具学习
lang: zh-CN
copy_from: 'https://blog.csdn.net/xhyu61/article/details/132457697'
abbrlink: 77604d4a
date: 2023-09-01 00:00:00
---

文章详细介绍了PyTorch中创建张量的多种方法，包括使用`torch.tensor()`，`torch.from_numpy()`，`torch.zeros()`，`torch.zeros_like()`，`torch.ones()`，`torch.ones_like()`，`torch.full()`，`torch.arange()`，`torch.linspace()`，`torch.logspace()`，`torch.eye()`，`torch.normal()`，`torch.randn()`，`torch.randn_like()`，`torch.rand()`，`torch.rand_like()`，`torch.randint()`，`torch.randint_like()`，`torch.randperm()`，`torch.bernoulli()`等函数。每个函数都包含了详细的用法示例，涵盖了常见的数据创建需求。视频链接提供了更加直观的讲解。

<!--more-->

参考视频：
[深度之眼官方账号：01-02-张量简介与创建](https://www.bilibili.com/video/BV1Gr4y1u7n8/?spm_id_from=333.880.my_history.page.click&vd_source=5e736c77221d69ad61b7490451a44145)

## torch.tensor()

```python
b = torch.tensor(data, 
				 dtype=None, 
				 device=None, 
				 requires_grad=False, 
				 pin_memory=False)
```
data：创建的tensor的数据来源，可以是list或numpy
dtype：数据类型，默认与data一致
device：所选设备，cuda/cpu
requires_grad：是否需要梯度
pin_memory：是否存在锁页内存（与转换效率有关，通常设置为False）

```python
import numpy as np
import torch

arr = np.ones((3, 3))
print("ndarray的数据类型：", arr.dtype)
t = torch.tensor(arr)
# t = torch.tensor(arr, device='cuda')
print(t)
```
输出：

```python
ndarray的数据类型： float64
tensor([[1., 1., 1.],
        [1., 1., 1.],
        [1., 1., 1.]], dtype=torch.float64)
```

## torch.from_numpy(ndarray)
功能：从numpy创建tensor
**注意**：从torch.from_numpy创建的tensor与原ndarray共享内存，当修改其中一个的数据，另一个也会被改动。

```python
import numpy as np
import torch

arr = np.array([[1, 2, 3], [4, 5, 6]])
t = torch.from_numpy(arr)
print(t)
arr[0, 0] = 7
print(t)
```
输出：

```python
tensor([[1, 2, 3],
        [4, 5, 6]], dtype=torch.int32)
tensor([[7, 2, 3],
        [4, 5, 6]], dtype=torch.int32)
```

## torch.zeros()
功能：根据给定size创建全0的tensor

```python
torch.zeros(*size, 
            out=None, 
            dtype=None, 
            layout=torch.strided, 
            device=None, 
            requires_grad=False)
```
*size：创建的tensor的尺寸
out：输出到哪个tensor当中
dtype：创建的tensor中内容的类型
layout：tensor在内存中的分布方式，目前支持`torch.strided`和`torch.sparse_coo`
device：所在设备，gpu/cpu
requires_grad：是否需要梯度

```python
import torch

t = torch.zeros((3, 3))     #不使用out参数创建
tt = torch.tensor([2.])
torch.zeros((3, 3), out=tt) #使用out参数输出到已有tensor
print(t, '\n', tt)
```
输出：

```python
tensor([[0., 0., 0.],
        [0., 0., 0.],
        [0., 0., 0.]])
 tensor([[0., 0., 0.],
        [0., 0., 0.],
        [0., 0., 0.]])
```
有一点需要注意，如果像这样使用：
```python
t = torch.tensor([2.])
p = torch.zeros((3, 3), out=t)
```
那么`t`和`p`会指向同一个地址。

## torch.zeros_like()
功能：根据input的形状创建全0的tensor。

```python
torch.zeros_like(input,
                 dtype=None,
                 layout=None,
                 device=None,
                 requires_grad=False)
```
input：创建新的tensor所用的形状的基准，生成的tensor和input的形状相同。
**注**：input只能为tensor，不能是np.array。
其余参数和torch.zeros()相同。

```python
import numpy as np
import torch

data = torch.tensor(np.array([[1, 2], [3, 4]]))
t = torch.zeros_like(data)
print(t)
```
输出：
```python
tensor([[0, 0],
        [0, 0]], dtype=torch.int32)
```

## torch.ones()
功能：根据给定size创建全1的tensor，与torch.zeros()基本一样。
```python
torch.ones(*size, 
           out=None,
           dtype=None,
           layout=torch.strided,
           device=None,
           requires_grad=False)
```

## torch.ones_like()
功能：根据input的形状创建全1的tensor，与torch.zeros_like()基本一样。

```python
torch.ones_like(input, 
           dtype=None,
           layout=None,
           device=None,
           requires_grad=False)
```

## torch.full()
功能：根据给定size创建tensor，元素全部赋值为fill_value。
```python
torch.full(size,
           fill_value,
           out=None,
           dtype=None,
           layout=torch.strided,
           device=None,
           requires_grad=False)
```
fill_value：传入的参数，full()将创建好的tensor全部赋值为fill_value。
```python
import numpy as np
import torch

t = torch.full(size=(3, 3), fill_value=4)
print(t)
```
输出：

```python
tensor([[4, 4, 4],
        [4, 4, 4],
        [4, 4, 4]])
```

## torch.arange()
功能：创建等差的一维tensor。

```python
torch.arange(start=0,
             end,
             step=1,
             out=None,
             dtype=None,
             layout=torch.strided,
             device=None,
             requires_grad=False)
```

范围是[start, end)，左闭右开的，step为步长。其他的参数与上面的函数一样。

```python
import numpy as np
import torch

t = torch.arange(start=3, end=6, step=1)
print(t)
```
输出：

```python
tensor([3, 4, 5])
```
## torch.linspace()
功能：创建均分的1维tensor。
linspace: Linear space，类比下面的logspace。

```python
torch.linspace(start=0,
               end,
               steps=100,
               out=None,
               dtype=None,
               layout=torch.strided,
               device=None,
               requires_grad=False)
```
范围是[start, end]，是闭区间（和arange不同）。steps为生成的tensor的长度。

```python
import numpy as np
import torch

t = torch.linspace(start=2, end=10, steps=4)
print(t)
```
输出：

```python
tensor([ 2.0000,  4.6667,  7.3333, 10.0000])
```
## torch.logspace()
功能：创建对数均分的1维tensor。
logspace: Log space，类比上面的linspace。

```python
torch.logspace(start,
               end,
               steps=100,
               base=10.0,
               out=None,
               dtype=None,
               layout=torch.strided,
               device=None,
               requires_grad=False)
```
start、end、steps与linspace相同。base指对数函数的底，默认为10。
和linspace不同的是，我们通过start, end, steps枚举出来的数列，是tensor的内容外面要套一个$log_{base}$。反过来讲，我们枚举的就是以base为底的那些幂数。
可以参考下面的代码：

```python
import numpy as np
import torch

t = torch.logspace(start=1, end=3, steps=3, base=10.0)
print(t)
```
输出：

```python
tensor([  10.,  100., 1000.])
```

## torch.eye()
功能：创建单位对角矩阵，是2维tensor。**默认为方阵**。

```python
torch.eye(n,
          m=None,
          out=None,
          dtype=None,
          layout=torch.strided,
          device=None,
          requires_grad=False)
```
n、m：矩阵行数、列数。通常我们只需要设置n。

```python
import numpy as np
import torch

t = torch.eye(n=3)
p = torch.eye(n=3, m=4)
print(t)
print(p)
```
输出：

```python
tensor([[1., 0., 0.],
        [0., 1., 0.],
        [0., 0., 1.]])
tensor([[1., 0., 0., 0.],
        [0., 1., 0., 0.],
        [0., 0., 1., 0.]])
```

## torch.normal()
功能：根据给定的均值$\mu$和标准差$\sigma$，通过$N(\mu,\sigma)$生成对应尺寸的随机数（具体如何对应尺寸，下面会有说明）。

```python
torch.normal(mean,
             std,
             out=None)
```
mean：均值；std：标准差。
均值和标准差的类型，既可以是float，也可以是tensor。此时出现了4种情况，先看示例代码。

```python
import numpy as np
import torch

t1 = torch.normal(mean=2, std=3, size=(2, 3))
print("t1:\n", t1)
t2 = torch.normal(mean=torch.arange(1., 3.), std=torch.arange(3., 5.))
print("t2:\n", t2)
t3 = torch.normal(mean=1., std=torch.arange(1., 5.))
print("t3:\n", t3)
t4 = torch.normal(mean=torch.arange(1., 4.), std=2.)
print("t4:\n", t4)
```
输出：

```python
t1:
 tensor([[-0.1495,  0.2061,  3.0486],
        [ 6.1257,  1.6023,  1.1515]])
t2:
 tensor([-5.4967,  7.4201])
t3:
 tensor([ 3.3218,  0.7347, -3.6644,  3.4812])
t4:
 tensor([2.2250, 1.1026, 0.9171])
```
#### mean和std都是float
见示例代码中t1。
此时我们必须要再加一个参数size，表示我们要生成的tensor的尺寸。生成的t1中每一个元素都是由$N(mean,std)$生成的一个随机数。

#### mean和std都是tensor
见示例代码中t2。
经过尝试，需要mean和std的shape相同，这样生成的tensor的对应位置就是由mean和std中对应位置的均值和标准差随机出来的数。
#### mean是float，std是tensor
见示例代码中t3。
生成的tensor和std的shape相同，对应的位置是由mean、std中的对应位置的标准差随机出来的数。
#### mean是tensor，std是float
见示例代码中t4。
生成的tensor和mean的shape相同，对应的位置是由mean中的对应位置的均值、std随机出来的数。

## torch.randn()
功能：生成标准正态分布$N(0,1)$。

```python
torch.randn(*size, 
            out=None,
            dtype=None,
            layout=torch.strided,
            device=None,
            requires_grad=False)
```

```python
import numpy as np
import torch

t = torch.randn((2, 3))
print(t)
```
输出：

```python
tensor([[ 0.2370, -1.4351, -0.0624],
        [ 0.7974,  1.2915, -1.0052]])
```

## torch.randn_like()
功能：生成与给定tensor同样尺寸的标准正态分布tensor。（类比torch.zeros_like()和torch.ones_like()）

```python
import numpy as np
import torch

tmp = torch.ones((2, 3))
t = torch.randn_like(tmp)
print(t)
```
输出：

```python
tensor([[-0.3384, -0.8061,  0.7020],
        [ 0.1602,  0.6525, -0.6860]])
```

## torch.rand()、torch.rand_like()
功能：在区间[0,1)上生成均匀分布，示例略。

## torch.randint()、torch.randint_like()
功能：在区间[low, high)生成**整数**均匀分布。

## torch.randperm()
功能：生成从0到n-1的随机排列，n是张量的长度。

```python
torch.randperm(n,
               out=None,
               dtype=torch.int64,
               layout=torch.strided,
               device=None,
               requires_grad=False)
```

```python
import numpy as np
import torch

t = torch.randperm(5)
print(t)
```
输出：

```python
tensor([3, 2, 1, 0, 4])
```

## torch.bernoulli()
功能：以input为概率，生成伯努利分布（0-1分布，两点分布）

```python
torch.bernoulli(input,
                *,
                generator=None,
                ont=None)
```
input为概率值，为tensor。

```python
import numpy as np
import torch

p = torch.rand((3, 3))
t = torch.bernoulli(p)
print("p:\n", p)
print("t:\n", t)
```
输出：

```python
p:
 tensor([[0.6881, 0.7921, 0.4212],
        [0.6857, 0.4809, 0.4009],
        [0.2400, 0.5160, 0.1303]])
t:
 tensor([[1., 1., 1.],
        [1., 1., 0.],
        [0., 0., 0.]])
```