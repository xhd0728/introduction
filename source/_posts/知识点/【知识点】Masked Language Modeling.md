---
title: Masked Language Modeling
tag:
  - NLP
category:
  - 论文学习
lang: zh-CN
abbrlink: c581aeed
date: 2023-11-21 00:00:00
---

Masked Language Modeling（MLM，遮蔽语言建模）是一种自然语言处理（NLP）中的预训练任务，其主要目标是通过在输入文本中遮蔽（掩盖）一些单词并尝试预测这些遮蔽单词的身份来学习词汇和语言表示。这一任务通常在Transformer等深度学习模型上进行。

<!--more-->

以下是Masked Language Modeling 的基本步骤：

1. **遮蔽输入文本：** 在输入文本中，随机选择一些单词，并将它们用特殊的标记（通常是"[MASK]"标记）替换。这使得模型在训练时需要预测这些被遮蔽的单词。

2. **模型预测：** 将经过遮蔽的文本输入模型，并让模型预测被遮蔽的位置上的单词。在Transformer模型中，这通常涉及到在每个位置上进行softmax分类，使得模型预测每个词汇表中的单词的概率。

3. **计算损失：** 通过比较模型的预测和真实的被遮蔽单词，计算一个损失值。损失函数通常是交叉熵损失。

通过这个过程，模型被迫学习对上下文的理解，因为它需要根据上下文来预测被遮蔽的单词。这种预训练任务可以为后续特定任务（如文本分类、命名实体识别等）提供有用的语言表示。

BERT（Bidirectional Encoder Representations from Transformers）是一个采用MLM作为预训练任务的例子。BERT通过预测被遮蔽的单词，以及利用双向上下文信息，获得了在多种NLP任务上出色表现的通用语言表示。其他一些模型，如GPT-2（Generative Pre-trained Transformer 2）也使用了类似的预训练任务，尽管采用的是单向语言模型。