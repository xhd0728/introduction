---
title: DataLoader、Dataset、自定义Dataset
tag:
  - PyTorch
  - 机器学习
category:
  - 工具学习
lang: zh-CN
copy_from: 'https://blog.csdn.net/xhyu61/article/details/133553713'
abbrlink: 9273cbd0
date: 2023-10-04 00:00:00
---

该文章介绍了PyTorch中`torch.utils.data.DataLoader`和`torch.utils.data.Dataset`的用法。`DataLoader`用于构建可迭代的数据加载器，支持多种参数设置，如`batch_size`、`shuffle`等。`Dataset`是一个抽象类，用户需要自定义类并继承它，实现`__getitem__`方法以返回样本。文章还展示了如何使用自定义的`Dataset`和`DataLoader`加载数据，并提供了相应的代码示例。

<!--more-->

参考视频：
[深度之眼官方账号 - 02-01 Dataloader与Dataset.mp4](https://www.bilibili.com/video/BV1Ya411X7XM)

## torch.utils.data.DataLoader
功能：构建可迭代的数据装载器。

```python
data.DataLoader(dataset,
                batch_size=1,
                shuffle=False,
                sampler=None,
                batch_sampler=None,
                num_workers=0,
                collate_fn=None,
                pin_memory=False,
                drop_last=False,
                timeout=0,
                worker_init_fn=None,
                multiprocessing_context=None)
```
dataset：Dataset类，决定数据从哪里读取；
batch_size：批大小；
shuffle：每个epoch是否乱序；
sampler：定义从数据集中抽取数据的策略，指明sampler时不能指明shuffle。
batch_sampler：和sampler相似，但是一次返回一个批大小的下标。和`batch_size`、`shuffle`	、`sampler`、`drop_last`互斥；
num_workers：使用该DataLoader加载数据的**子进程**的数量。如果该值为0，意味着只有主进程使用该DataLoader加载数据。
collate_fn：合并样本列表，形成一个小型tensor批次。在使用map-style dataset批量加载时使用。
pin_memory：True时，DataLoader在返回tensor数据时会先将这些数据复制到device/CUDA的pinned memory中。
drop_last：当样本数不能被batch_size整除时，是否舍弃最后一批数据。
timeout：如果是正数，则表示获取批次的超时值。要求设置为非负数。
worker_init_fn：如果不是None，那么这个函数会调用所有带有worker_id的worker的子进程。

> **Epoch**：所有训练样本都已输入到模型中，称为一个Epoch。
> **Iteration**：一批样本输入到模型中，称为一个Iteration。
> **BatchSize**：批大小，决定一个Epoch有多少个Iteration。

## torch.utils.data.Dataset
功能：Dataset抽象类，所有自定义的Dataset需要继承他，并且复写`__getitem__()`。

\_\_getitem\_\_()：接收一个索引，返回一个样本。

### torch.utils.data.TensorDataset
由tensor封装的数据集，传入一些在第1维长度相同的tensor，形成dataset。

## DataLoader如何使用？

```python
import torch
from torch.utils import data


def data_gen(num_examples):
    X = torch.normal(0, 1, (num_examples, 2))
    y = torch.normal(0, 1, (num_examples, 1))
    return X, y


def load_array(data_arrays, batch_size, need_shuffle=True):
    dataset = data.TensorDataset(*data_arrays)
    return data.DataLoader(dataset, batch_size, shuffle=need_shuffle)


data_A, data_B = data_gen(30)
# 定义一个数据迭代器data_iter
data_iter = load_array((data_A, data_B), 10, False)

# 使用for循环，iter每次会指向下一组数据
# 每组数据返回我们封装进去的那些tensor，封装几个就返回几个
for X, y in iter(data_iter):
    print("X\n", X)
    print("y\n", y)
```
输出：

```python
X
 tensor([[-0.2338, -0.4072],
        [ 1.6080, -0.0880],
        [-1.0243, -0.6245],
        [ 0.6012,  1.9620],
        [ 1.1876,  0.9539],
        [-0.5972,  0.9251],
        [-0.4918, -0.1340],
        [ 2.3297,  0.1833],
        [-0.8487,  0.3370],
        [-0.0600,  2.3769]])
y
 tensor([[ 1.0454],
        [ 0.5992],
        [ 2.0075],
        [ 0.2727],
        [-1.6845],
        [ 0.0845],
        [ 1.0992],
        [ 0.5103],
        [-0.6727],
        [-0.1900]])
X
 tensor([[-0.1419, -1.5535],
        [-1.6436,  0.8680],
        [-0.4432, -0.7703],
        [ 0.3822,  0.4675],
        [ 0.4000,  1.3471],
        [ 0.9776,  2.0103],
        [ 0.1298,  2.7382],
        [ 0.2664, -0.6223],
        [-1.0774,  0.0734],
        [-0.1904, -1.3299]])
y
 tensor([[-0.5979],
        [-0.5432],
        [ 0.2951],
        [ 0.2811],
        [-0.5997],
        [ 0.8073],
        [ 1.4356],
        [ 1.1555],
        [-0.3368],
        [-0.0626]])
X
 tensor([[-1.4326, -0.3407],
        [-1.1878, -1.5619],
        [ 0.3498,  1.5307],
        [-0.8174,  0.6017],
        [ 0.8076,  0.8295],
        [ 2.6239,  1.1669],
        [-1.2598,  1.4309],
        [ 0.3365,  0.1765],
        [-0.4472, -0.6882],
        [ 0.6732, -0.0742]])
y
 tensor([[ 0.5114],
        [ 1.0669],
        [-1.5565],
        [ 0.4512],
        [ 3.2071],
        [ 0.4752],
        [-1.5981],
        [ 0.0035],
        [-0.2723],
        [-1.3634]])
```

## 自定义Dataset

```python
import torch
from torch.utils import data

# 自定义的Dataset
# 每一条数据包含X的一行数据(1x2的tensor)和y的一行数据(1x1的tensor)
class MyTensorDataset(data.Dataset):
    def __init__(self, tensor_list):
        # 初始化超类 (data.Dataset)
        super(MyTensorDataset, self).__init__()
        self.data = tensor_list

    
    def __getitem__(self, index):
        # 这个函数必须重写
        # 返回dataset中下标编号为index的数据
        sample = {'X': self.data[0][index], 'y': self.data[1][index]}
        return sample
    

    def __len__(self):
        # 返回这个dataset中的数据个数
        return len(self.data[0])


# 随机生成数据
def data_gen(num_examples):
    X = torch.normal(0, 1, (num_examples, 2))
    y = torch.normal(0, 1, (num_examples, 1))
    return X, y


# 使用自己的Dataset
def load_array(data_arrays, batch_size, need_shuffle=True):
    X, y = data_arrays  
    dataset = MyTensorDataset((X, y)) 
    return data.DataLoader(dataset, batch_size, shuffle=need_shuffle)


data_A, data_B = data_gen(30)
data_iter = load_array((data_A, data_B), 10, False)

for sample in iter(data_iter):
    print("sample:\n", sample)
```
输出：

```python
sample:
 {'X': tensor([[ 0.2149,  0.6216],
        [ 0.4691,  0.1862],
        [-1.7705, -1.5983],
        [-0.0196, -0.5903],
        [-0.1313,  0.3206],
        [ 0.1898, -0.2575],
        [ 1.5934, -0.4720],
        [-0.8343, -0.2181],
        [ 1.6159, -0.5473],
        [-1.2662, -0.0218]]), 'y': tensor([[ 0.8886],
        [ 0.1653],
        [ 0.8054],
        [-0.0725],
        [-0.4806],
        [-0.4661],
        [-0.4040],
        [ 0.6192],
        [-0.2522],
        [ 1.4091]])}
sample:
 {'X': tensor([[ 0.6441, -0.5759],
        [-0.7285, -1.0021],
        [ 0.1250, -0.2333],
        [ 0.3196,  0.7762],
        [ 0.1429,  0.4667],
        [ 1.0751, -0.4867],
        [ 0.1664,  0.3489],
        [ 0.1616, -0.1998],
        [ 0.6707, -0.4678],
        [-1.7778, -2.4658]]), 'y': tensor([[-0.2342],
        [ 0.1402],
        [ 0.5768],
        [ 1.7898],
        [-0.6802],
        [-2.3584],
        [ 0.7048],
        [ 0.1848],
        [ 0.1225],
        [ 0.5535]])}
sample:
 {'X': tensor([[ 0.1694,  0.2863],
        [ 0.3062, -0.7494],
        [ 0.6844,  1.9278],
        [ 0.9141,  0.3842],
        [-1.2314, -1.4933],
        [ 0.1568, -1.4182],
        [ 1.7723, -0.4890],
        [ 0.5734,  1.0614],
        [ 0.9536, -0.2866],
        [ 0.2510,  0.3375]]), 'y': tensor([[ 0.9802],
        [-0.5557],
        [ 0.7763],
        [ 1.1688],
        [-1.0067],
        [-0.4044],
        [-0.2745],
        [-0.3661],
        [-0.6058],
        [ 0.6905]])}
```
