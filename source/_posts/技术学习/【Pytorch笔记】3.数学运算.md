---
title: 数学运算
tag:
  - PyTorch
  - 机器学习
category:
  - 技术学习
lang: zh-CN
copy_from: 'https://blog.csdn.net/xhyu61/article/details/133044585'
abbrlink: b8dbd0ce
date: 2023-09-27 00:00:00
---

文章介绍了PyTorch中常用的张量操作函数，包括逐元素计算的`torch.add()`、`torch.sub()`、`torch.mul()`、`torch.div()`，以及复合操作函数`torch.addcmul()`、`torch.addcdiv()`。此外，还介绍了数学运算函数`torch.log()`、`torch.log10()`、`torch.log2()`、`torch.exp()`、`torch.pow()`，以及三角函数和双曲函数的运算函数，如`torch.abs()`、`torch.acos()`、`torch.cosh()`、`torch.cos()`、`torch.asin()`、`torch.atan()`、`torch.atan2()`等。每个函数都附有详细的使用示例。视频链接提供了更详细的讲解。

<!--more-->

参考视频：
[深度之眼官方账号 - 01-03-mp4-张量操作与线性回归](https://www.bilibili.com/video/BV1QB4y1v7br/?spm_id_from=333.788&vd_source=5e736c77221d69ad61b7490451a44145)

## torch.add()
功能：逐元素计算`input+alpha×other`。

```python
torch.add(input,
          alpha=1,
          other,
          out=None)
```
input：tensor；
alpha：other的系数，是个实数；
other：和input同样形状的tensor。

```python
import torch

t1 = torch.tensor([[2, 3], [4, 5]])
t2 = torch.tensor([[1, 1], [2, 2]])
t = torch.add(t1, alpha=2, other=t2)
print(t)
```
输出：

```python
tensor([[4, 5],
        [8, 9]])
```

## torch.sub()
功能：逐元素计算`input-alpha×other`。

```python
torch.sub(input,
          alpha=1,
          other,
          out=None)
```
input：tensor；
alpha：other的系数，是个实数；
other：和input同样形状的tensor。

```python
import torch

t1 = torch.tensor([[2, 3], [4, 5]])
t2 = torch.tensor([[1, 1], [2, 2]])
t = torch.add(t1, alpha=2, other=t2)
print(t)
```
输出：

```python
tensor([[0, 1],
        [0, 1]])
```

## torch.mul()
功能：逐元素计算$out_i=input_i \times other_i$
```python
torch.mul(input,
          other)
```
input：tensor；
other：和input同样尺寸的tensor。
**other支持广播，即可以只向other传入一个数，torch利用广播机制变成同样尺寸的tensor。**

```python
import torch

t1 = torch.tensor([[9, 12], [15, 18]])
t2 = torch.tensor([[3, 3], [2, 2]])
t = torch.mul(t1, other=t2)
print(t)
```
输出：

```python
tensor([[27, 36],
        [30, 36]])
```

## torch.div()
功能：逐元素计算$out_i=\frac{input_i}{other}$

```python
torch.div(input,
          other)
```
input：tensor；
other：和input同样尺寸的、元素不能为0的tensor。
**other支持广播，即可以只向other传入一个数，torch利用广播机制变成同样尺寸的tensor。**

```python
import torch

t1 = torch.tensor([[9, 12], [4, 6]])
t2 = torch.tensor([[3, 3], [2, 2]])
t = torch.div(t1, other=t2)
print(t)
```
输出：

```python
tensor([[3., 4.],
        [2., 3.]])
```

## torch.addcmul()
功能：逐元素计算$out_i=input_i+value \times tensor1_i \times tensor2_i$

```python
torch.addcmul(input,
              value=1,
              tensor1,
              tensor2,
              out=None)
```
input：输入的tensor；
value：见公式，实数；
tensor1：和input相同形状的tensor，见公式；
tensor2：和input相同形状的tensor，见公式。

```python
import torch

t1 = torch.tensor([[2., 3.], [4., 5.]])
t2 = torch.tensor([[4., 6.], [8., 10.]])
t3 = torch.tensor([[2., 2.], [2., 2.]])
t = torch.addcmul(t1, value=2, tensor1=t2, tensor2=t3)
print(t)
```
输出：
```python
tensor([[18., 27.],
        [36., 45.]])
```

## torch.addcdiv()
功能：逐元素计算$out_i=input_i+value \times\frac{tensor1_i}{tensor2_i}$。
```python
torch.addcdiv(input,
              value=1,
              tensor1,
              tensor2,
              out=None)
```
input：输入的tensor；
value：见公式，实数；
tensor1：和input相同形状的tensor，见公式；
tensor2：和input相同形状但是元素中不能出现0的tensor，见公式。
**注：input、tensor1、tensor2的内容需要是浮点型。如果使用整数会报如下错误：**

> RuntimeError: Integer division with addcdiv is no longer supported, and in a future  release addcdiv will perform a true division of tensor1 and tensor2. The historic addcdiv behavior can be implemented as (input + value * torch.trunc(tensor1 / tensor2)).to(input.dtype) for integer inputs and as (input + value * tensor1 / tensor2) for float inputs. The future addcdiv behavior is just the latter implementation: (input + value * tensor1 / tensor2), for all dtypes.

```python
import torch

t1 = torch.tensor([[2., 3.], [4., 5.]])
t2 = torch.tensor([[4., 6.], [8., 10.]])
t3 = torch.tensor([[2., 2.], [2., 2.]])
t = torch.addcdiv(t1, value=2, tensor1=t2, tensor2=t3)
print(t)
```
输出：

```python
tensor([[ 6.,  9.],
        [12., 15.]])
```

## torch.log()
功能：逐元素求解$out_i=log_e(input_i)$。

```python
torch.log(input,
		  out=None)
```
input：待求解的tensor。

```python
import torch

t1 = torch.tensor([[9., -12.], [15., 18.]])
t = torch.log(t1)
print(t)
```
输出：

```python
tensor([[2.1972,    nan],
        [2.7081, 2.8904]])
```

## torch.log10()
功能：逐元素求解$out_i=log_{10}(input_i)$。

```python
torch.log10(input,
			out=None)
```
input：待求解的tensor。

```python
import torch

t1 = torch.tensor([[9., -12.], [15., 18.]])
t = torch.log10(t1)
print(t)
```
输出：

```python
tensor([[0.9542,    nan],
        [1.1761, 1.2553]])
```
## torch.log2()
功能：逐元素求解$out_i=log_2(input_i)$。

```python
torch.log2(input,
		   out=None)
```
input：待求解的tensor。

```python
import torch

t1 = torch.tensor([[8., -12.], [16., 18.]])
t = torch.log2(t1)
print(t)
```
输出：

```python
tensor([[3.0000,    nan],
        [4.0000, 4.1699]])
```

## torch.exp()
功能：逐元素求解$out_i=e^{input_i}$。
```python
torch.exp(input,
		  out=None)
```
input：待求解的tensor。

```python
import math
import torch

t1 = torch.tensor([[-2., 0.], [1., math.log(2.)]])
t = torch.exp(t1)
print(t)
```
输出：

```python
tensor([[0.1353, 1.0000],
        [2.7183, 2.0000]])
```

## torch.pow()
功能：逐元素求解$out_i=x_i^{exponent_i}$

```python
torch.pow(input,
          exponent,
          out=None)
```
input：待求解的tensor。
exponent：与input相同形状的tensor。
**如果exponent是一个数，torch会广播成一个和input相同形状的tensor。**

```python
import torch

t1 = torch.tensor([[1., 2.], [3., 4.]])
t2 = torch.tensor([[3., 2.], [4., 2.]])
t3 = torch.pow(t1, 2.)
t4 = torch.pow(t1, t2)
print(t3)
print(t4)
```
输出：
```python
tensor([[ 1.,  4.],
        [ 9., 16.]])
tensor([[ 1.,  4.],
        [81., 16.]])
```


## tensor.abs()
功能：逐元素取绝对值，$out_i=|input_i|$。

```python
torch.abs(input,
          out=None)
```
input：待求解的tensor。

```python
import torch

t1 = torch.tensor([[1., -2.], [-3., 4.]])
t = torch.abs(t1)
print(t)
```
输出：

```python
tensor([[1., 2.],
        [3., 4.]])
```

## tensor.acos()
功能：逐元素求解$out_i=cos^{-1}(input_i)$。

```python
torch.acos(input,
           out=None)
```
input：待求解的tensor。

```python
import torch

t1 = torch.randn(4)
print(t1)
t = torch.acos(t1)
print(t)
```

输出：
```python
tensor([ 0.5100,  0.1678, -0.0250,  0.3119])
tensor([1.0357, 1.4022, 1.5958, 1.2536])
```

## torch.cosh()
功能：逐元素求解$out_i=cosh(input_i)$
注：$cosh(x)=\frac{e^x+e^{-x}}{2}$

```python
torch.cosh(input,
           out=None)
```
input：待求解的tensor。

```python
import torch

t1 = torch.randn(4)
print(t1)
t = torch.cosh(t1)
print(t)

torch.cosh(input,
           out=None)
```
输出：
```python
tensor([-0.3447, -0.2875, -0.2717, -1.3635])
tensor([1.0600, 1.0416, 1.0371, 2.0828])
```

## torch.cos()
功能：逐元素求解$out_i=cos(input_i)$

```python
torch.cos(input,
           out=None)
```
input：待求解的tensor。

```python
import torch

t1 = torch.randn(4)
print(t1)
t = torch.cos(t1)
print(t)

torch.cosh(input,
           out=None)
```
输出：
```python
tensor([-0.6443, -0.8991,  1.2432, -0.3162])
tensor([0.7995, 0.6223, 0.3218, 0.9504])
```

## torch.asin()
功能：逐元素求解$out_i=sin^{-1}(input_i)$
```python
torch.asin(input,
           out=None)
```
input：待求解的tensor。
```python
import torch

t1 = torch.randn(4)
print(t1)
t = torch.asin(t1)
print(t)
```
输出：

```python
tensor([-0.7372, -0.0238, -1.8213, -0.0912])
tensor([-0.8289, -0.0238,     nan, -0.0913])
```

## torch.atan()
功能：逐元素求解$out_i=tan^{-1}(input_i)$
```python
torch.atan(input,
           out=None)
```
input：待求解的tensor。
```python
import torch

t1 = torch.randn(4)
print(t1)
t = torch.atan(t1)
print(t)
```
输出：

```python
tensor([ 0.3620, -0.6551,  1.0304,  2.1545])
tensor([ 0.3474, -0.5799,  0.8003,  1.1362])
```

## torch.atan2()
功能：逐元素求解$out_i=tan^{-1}(\frac{input_i}{other_i})$
```python
torch.atan(input,
		   other,
           out=None)
```
input：待求解的tensor。
```python
import torch

t1 = torch.randn(4)
print(t1)
t2 = torch.randn(4)
print(t2)
t = torch.atan2(t1, t2)
print(t)
```
输出：

```python
tensor([ 1.9372,  0.7993, -1.4123,  0.4260])
tensor([-1.5106,  1.2147, -1.4479,  0.1674])
tensor([ 2.2331,  0.5820, -2.3686,  1.1963])
```