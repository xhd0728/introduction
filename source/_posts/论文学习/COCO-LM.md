---
title: >-
  COCO-LM: Correcting and Contrasting Text Sequences for Language Model
  Pretraining
tag:
  - 预训练模型
  - 信息检索
category:
  - 论文学习
lang: zh-CN
copy_from: 'https://zhuanlan.zhihu.com/p/353624306'
abbrlink: a5e8b87b
date: 2023-11-25 00:00:00
---

最近在arxiv上看见一篇关于预训练模型的文章，觉得很不错，这里附上链接: [arXiv](https://arxiv.org/abs/2102.08473)

<!--more-->

该篇文章2021年2月16日上传，提出了一种新的预训练模型的框架，个人认为COCO-LM结合了许多当下比较新进的思想，在后bert时代，一定程度上突破了对BERT模型传统的预训练方法。

![](https://cdn.jsdelivr.net/gh/xhd0728/oss-github-picgo-repository/picgo/20231125154307.png)

那么，这篇文章主要结合了哪些思想呢？

![](https://cdn.jsdelivr.net/gh/xhd0728/oss-github-picgo-repository/picgo/20231125154409.png)

1.第一点，我认为COCO-LM框架延续了ELECTRA预训练模型的思想。ELECTRA预训练模型主要应用了GAN对抗神经网络的思想，不了解的小伙伴们可以参考一些其他资料，这里我简单说一下我的理解。

GAN对抗神经网络在CV领域上应用比较成熟，在CV的应用上GAN主要包括两个神经网络模型：一个是生成式模型G，一个是判别式模型D。生成式模型的作用是通过随机噪声生成和原始样本相似的数据(注意这里是通过随机噪声)，判别式模型的作用是判断给定的实例是真实实例还是人为伪造的(也就是生成式模型所生成的)。那么这里就包含了对抗的思想，即生成式模型的目的是能够生成欺骗判别式模型的实例，判别式模型的目的是判别给定的实例是否是人为伪造的。

[图解 生成对抗网络GAN 原理 超详解](https://blog.csdn.net/DFCED/article/details/105175097)

ELECTRA当中引用了这样的“对抗”思想，将判别式模型引入到了模型的预训练之中。像BERT、ROBERTA、XLNET等等预训练模型都属于生成式模型，在输入上用 [MASK] 遮蔽掉部分 tokens，再训练一个模型以重建出原始的 tokens。而ELECTRA预训练模型使用了判别式模型，其效果也出乎意料的好。

ELECTRA模型的主要思想也是包括了两个神经网络模型：一个生成式模型G，一个生成式模型D。生成式模型G是MLM(Masked Language Model)模型，给定一个真实样例(GAN的生成式模型给定的是随机噪声)，用 [MASK] 遮蔽掉部分 tokens，生成替换的tokens；判别式模型D判断输入中每个 token 是否是由生成器生成。其过程如图所示：

![](https://cdn.jsdelivr.net/gh/xhd0728/oss-github-picgo-repository/picgo/20231125154846.png)

通过实验表明这种新的预训练任务比 MLM 更高效，该任务定义于全部的输入 tokens，而非仅仅被遮蔽掉的那一部分小小的输入子集。

在COCO-LM模型中Corrective Language Modeling (CLM)也延续了这样的思想。

2.COCO-LM模型引入了对比学习的思想，我认为是非常非常棒的创新点。最近刚好再看对比学习的相关paper，更多的是在CV领域中使用了对比学习，而COCO-LM刚好将对比学习带入到了NLP领域中。

什么是对比学习呢？

对比学习是一种自监督的学习方法。其主要思想我的理解是，把正样本距离拉近，正样本与负样本距离拉远。对比学习的例子如下：

- 给每个例子绘制两个独立的增强函数
- 使用两种增强机制，为每个示例生成两个互相关联的视图
- 让相关视图互相吸引，同时排斥其他示例

![](https://cdn.jsdelivr.net/gh/xhd0728/oss-github-picgo-repository/picgo/20231125154953.png)

如上图，$(Z_1,Z_2),(Z_3,Z_4) \dots (Z_{2n-1},Z_{2n})$这些可以看作正例对，而$Z1$可以与除$Z1$、$Z2$的任何实例组成负例对，如$(Z1,Z3),(Z1,Z4)$等等。那么这样一个实例$X$，在一个大小为$N$的$batch$里便可以产生一个正例，以及$N-1$个负例，那么这个 $loss$ 就可以看做是一个 $N$ 分类问题，实际上就是一个交叉熵，由此可以进行网络模型的训练。