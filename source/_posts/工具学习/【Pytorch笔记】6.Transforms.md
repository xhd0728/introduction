---
title: Transforms
tag:
  - PyTorch
  - 机器学习
category:
  - 工具学习
lang: zh-CN
copy_from: 'https://blog.csdn.net/xhyu61/article/details/133579397'
abbrlink: fc43a10f
date: 2023-10-06 00:00:00
---

PyTorch官方文档中介绍了`torchvision.transforms`的图像处理工具，包括几何变换、色彩变换、变换组合等。其中，几何变换包括Resize、RandomCrop、CenterCrop等，色彩变换有ColorJitter、Grayscale、GaussianBlur等。此外，文档还介绍了变换的组合方法，如Compose和RandomApply，以及其他杂项变换和格式转换方法。这些工具有助于构建强大的数据预处理管道，提升计算机视觉模型的性能。

<!--more-->

[pytorch官方文档 - transforms](https://pytorch.org/vision/stable/transforms.html)

transforms需要使用计算机视觉工具包：torchvision。
`torchvision.transforms`：常用的图像预处理方法；
`torchvision.datasets`：常用数据集的dataset实现，如MNIST、CIFAR-10、ImageNet等；
`torchvision.model`：常用的模型预训练，如AlexNet、VGG、ResNet、GoogleNet等。

## torchvision.transforms
常用的图像预处理方法，包括数据中心化、数据标准化、缩放、裁剪、旋转、翻转、填充、噪声添加、灰度变换、线性变换、仿射变换、亮度变换、饱和度变换、对比度变换等。

## Geometry - 几何变换
### torchvision.transforms.Resize()
功能：将给定图像缩放成指定的尺寸。

```python
trans = transforms.Resize((32, 32))
```
意思就是把样本图像缩放成32x32的。如果里面的参数给的不是上面这样的`(H, W)`的格式，而是一个数的话，那么令原图像的较短边与该数匹配，按比例缩放。

### torchvision.transforms.RandomCrop()
功能：将给定图像按照指定的尺寸随机裁剪。

```python
trans = transforms.RandomCrop(size=10, padding=4)
```
size指裁剪大小。如果`size=10`，就是裁出10x10的图片；如果`size=(15, 10)`，就是裁出15x10的图片。
padding指填充大小。如果`padding=4`，就是上下左右填充都是4；如果`padding=(4,2)`，就是左右填充为4，上下填充为2；如果`padding=(4,3,2,1)`，那么左填充为4，上填充为3，右填充为2，下填充为1。
### torchvision.transforms.CenterCrop()
功能：将给定图像按照指定的尺寸在中心裁剪。

```python
trans = transforms.CenterCrop(size=4)
```
size指裁剪大小。如果`size=4`，就是裁出4x4的图片；如果`size=(4, 3)`，就是裁出4x3的图片。
### torchvision.transforms.FiveCrop()
功能：将给定图像按照指定的尺寸在中心、四个角裁剪，返回五个裁剪出来的图。

```python
trans = transforms.FiveCrop(size=4)
```
size指裁剪大小。如果`size=4`，就是裁出4x4的图片；如果`size=(4, 3)`，就是裁出4x3的图片。
### torchvision.transforms.TenCrop()
功能：将给定图像按照指定的尺寸在中心、四个角裁剪，并分别进行翻转，返回十个裁剪/翻转出来的图。

```python
trans = transforms.TenCrop(size=4, vertical_flip=False)
```
size指裁剪大小。如果`size=4`，就是裁出4x4的图片；如果`size=(4, 3)`，就是裁出4x3的图片。
vertical_flip为True，则翻转采用垂直翻转；False，则翻转采用水平翻转。
### torchvision.transforms.Pad()
功能：将给定图像按照给定值进行边界填充。

```python
trans = transforms.Pad(padding=4, fill=0, padding_mode='constant')
```
padding指填充大小。如果`padding=4`，就是上下左右填充都是4；如果`padding=(4,2)`，就是左右填充为4，上下填充为2；如果`padding=(4,3,2,1)`，那么左填充为4，上填充为3，右填充为2，下填充为1。
fill指填充内容，该参数当且仅当后面的`padding_mode`为`'constant'`时有效，默认值为0。如果是一个长度为3的tuple，则分别用于填充图像的R,G,B层。用于tensor时仅支持一个数，用于PIL图像时仅支持一个数或一个长度为3的tuple。
padding_mode指填充方式，有以下取值：
`'constant'`：用一个常值填充，填充的内容用fill参数传入。
`'edge'`：边缘填充，使用图像最靠边界的元素的数值进行填充。
`'reflect'`：镜像填充，以最外侧的元素为对称轴，将图像的内容对称填充进去。如原图像为[1,2,3,4]、padding=2时，填充后变成[3,2,1,2,3,4,3,2]。
`'symmetric'`：对称填充，以边界为对称轴，将图像的内容对称填充进去。如原图像为[1,2,3,4]、padding=2时，填充后变成[2,1,1,2,3,4,4,3]。
### torchvision.transforms.RandomRotation()
功能：将给定图像按照指定角度范围进行随机旋转。

```python
trans = transforms.RandomRotation(degrees=(40, 90), 
								  interpolation=InterpolationMode.NEAREST,
								  expand=False,
								  center=None,
								  fill=0)
```
degrees指角度范围，如果`degree=40`，那么旋转角度的范围为`(-40,40)`；
interpolation指插值方式，有两种取值：
`InterpolationMode.NEAREST`：就近插值、
`InterpolationMode.BILINEAR`：双线性插值；
expand指是否扩充输出图像的尺寸，如果为True，则输出图像会根据旋转后的结果使尺寸变大，如果为False，则输出图像的形状不变。
center指旋转中心，默认为None，以图片左上角为旋转中心。
fill指旋转后空白的地方的填充内容。

### torchvision.transforms.RandomPerspective()
功能：将给定的图像按照给定参数进行透视变换（透视变换效果见下图）
![在这里插入图片描述](https://img-blog.csdnimg.cn/9c9cf6c1b86f4f679dc383b8aac6dd2e.png)

```python
trans = transforms.RandomPerspective(distortion_scale=0.5, 
									 p=0.5, 
									 interpolation=InterpolationMode.BILINEAR, 
									 fill=0)
```
distoration_scale指扭曲程度，范围是[0,1]，默认为0.5；
p指被变换概率，默认为0.5；
interpolation指插值方式，有两种取值：
`InterpolationMode.NEAREST`：就近插值、
`InterpolationMode.BILINEAR`：双线性插值；
fill指旋转后空白的地方的填充内容。
### torchvision.transforms.RandomHorizontalFlip()
功能：以指定概率水平翻转图像。

```python
trans = transforms.RandomHorizontalFlip(p=0.5)
```
p指被变换概率，默认为0.5；
### torchvision.transforms.RandomVerticalFlip()
功能：以指定概率垂直翻转图像。

```python
trans = transforms.RandomVerticalFlip(p=0.5)
```
p指被变换概率，默认为0.5；
## Color - 色彩变换
### torchvision.transforms.ColorJitter()
功能：随机改变图像的亮度、对比度、饱和度和色调。

```python
trans = transforms.ColorJitter(brightness=0.5, contrast=0.5, saturation=0.5, hue=0.5)
```
brightness指亮度：
如果是一个float，则亮度从`[max(0, 1-brightness), 1+brightness]`中均匀抽取，
如果是一个长度为2的tuple：float(min, max)，则亮度从`[min, max]`中均匀抽取；
contrast指对比度：
如果是一个float，则对比度从`[max(0, 1-contrast), 1+contrast]`中均匀抽取，
如果是一个长度为2的tuple：float(min, max)，则对比度从`[min, max]`中均匀抽取；
saturation指饱和度：
如果是一个float，则饱和度从`[max(0, 1-saturation), 1+saturation]`中均匀抽取，
如果是一个长度为2的tuple：float(min, max)，则饱和度从`[min, max]`中均匀抽取；
hue指色调：
如果是一个float，则色调从`[-hue, hue]`中均匀抽取，其中$0\leq hue \leq 0.5$，
如果是一个长度为2的tuple：float(min, max)，则色调从`[min, max]`中均匀抽取，其中$-0.5\leq min\leq max\leq 0.5$；
### torchvision.transforms.Grayscale()
功能：将图像转为灰度图像。

```python
trans = transforms.Grayscale(num_output_channels=1)
```
num_output_channels指输出图像的通道数，取值只能是1或3。
如果为1：返回的图像只有一个通道；
如果为3：返回的图像有3个通道，且`r=g=b`。
### torchvision.transforms.RandomGrayscale()
功能：以指定概率将图像转为灰度图像。

```python
trans = transforms.RandomGrayscale(p=0.5)
```
p为转变图像的概率。
### torchvision.transforms.GaussianBlur()
功能：使用随机的高斯模糊对图像进行模糊处理。

```python
trans = transforms.GaussianBlur(kernel_size=2, sigma=(0.1, 2.0))
```
kernel_size指高斯核的大小；
sigma指用于创建内核进行模糊处理的标准偏差，如果是一个float则sigma固定；如果是长度为2的float元组，令其为float(min, max)，并在`[min, max]`范围内均匀随机选取sigma。
### torchvision.transforms.RandomInvert()
功能：以指定概率替换图片的颜色。

```python
trans = transforms.RandomInvert(p=0.5)
```
p为改变颜色的概率。
### transforms.RandomPosterize()
功能：以指定概率将图片海报化（减少通道中的比特数）。

```python
trans = transforms.RandomPosterize(bits=4, p=0.5)
```
bits指每个通道保持的比特数，取值为0-8；
p为转化图像的概率。
### transforms.RandomSolarize()
功能：以指定概率将图片高曝（Solarize该怎么翻译呢？）。
```python
trans = transforms.RandomSolarize(threshold=128, p=0.5)
```
threshold指高曝的阈值。所谓高曝，就是将像素值在[0, threshold]映射到[threshold, 255]上。
p为转化图像的概率。

## Composition - 变换组合
### torchvision.transforms.Compose()
功能：将多个transforms变换封装成一个组合。
```python
transforms.Compose([
    transforms.CenterCrop(10),
    transforms.PILToTensor(),
    transforms.ConvertImageDtype(torch.float),
])
```
参数就是包含很多transforms变换的list。
### torchvision.transforms.RandomApply()
功能：以给定概率将transforms列表中的变换打乱。
```python
transforms.RandonApply([
    transforms.CenterCrop(10),
    transforms.PILToTensor(),
    transforms.ConvertImageDtype(torch.float),
], p=3)
```
第一项可以是transforms的sequence，也可以是torch.nn.ModuleList；
第二项p是打乱的概率。
## Miscellaneous - 杂项
### torchvision.transforms.Normalize()
功能：对得到的tensor进行减均值除标准差处理。

```python
trans = transforms.Normalize(mean=0, std=1, inplace=True)
```
mean指均值；
std指标准差；
inplace指是否替换原tensor，True为替换，False为不替换。
### torchvision.transforms.RandomErasing()
功能：对得到的图像，以一定概率随机一个矩形区域进行擦除。

```python
trans = transforms.RandomErasing(p=0.5, 
								 scale=(0.02, 0.33), 
								 ratio=(0.3, 3.3), 
								 value=0, 
								 inplace=False)
```
p指给定的概率；
scale指擦除区域面积占原图面积的比例范围；
ratio指擦除区域长宽比的范围；
value指擦除后填充的值，如果是一个int，那么全部用这个int来填充；如果是长度为3的int，则分别用于填充R、G、B三个通道；如果`value='random'`，则随机填充。
inplace指是否替换原tensor，True为替换，False为不替换。
## Conversion - 格式转换
### torchvision.transforms.ToTensor()
功能：将PIL图像或ndarray转换成tensor并将值映射到[0,1]之间。
更具体地，将一个PIL图像或ndarray（形状是$H\times W\times C$，元素范围在[0,255])转化成tensor（形状是$C\times H\times W$，元素范围在[0,1]）。前提是PIL图像属于L, LA, P, I, F, RGB, YCbCr, RGBA, CMYK, 1中的一种模式、ndarray的dtype=uint8。
其他情况下返回的tensor不会被映射到[0,1]之间。

```python
trans = transforms.ToTensor()
```
### torchvision.transforms.PILToTensor()
功能：将$H\times W\times C$的PIL图像转换为$C\times H\times W$的tensor。
```python
trans = transforms.PILToTensor()
```