---
title: GLUE
tag:
  - NLP
  - 信息检索
category:
  - 论文学习
lang: zh-CN
abbrlink: 9f7dd732
date: 2023-11-13 00:00:00
---

GLUE和SUPER-GLUE是用于衡量自然语言处理模型表现的两个基准测试体系:
<!--more-->
GLUE(General Language Understanding Evaluation):
- 由谷歌 AI 实验室于2018年提出。
- 包括9个不同类型的NLP任务,如句子推理、naming等。 
- 旨在评估模型在各种常见NLP任务上的泛化能力。

SUPER-GLUE:
- 是GLUE基准测试的升级版,由同一团队于2019年发布。
- 增加和改进了一些GLUE任务,加入了更难更复杂的新任务。
- 任务包括:想象性问答、多项选择、修辞关系等难点任务。
- 对模型的理解和推理能力要求更高。

两个基准测试的主要区别:
- SUPER-GLUE的任务设置更难,对模型的要求更高。
- 可以更全面和细致地检测模型在不同程度的NLP任务中的表现。
- 成为评估新一代更强大NLP模型的主流标准,如BERT、GPT-3等。

因此,在最新的研究中,模型表现都以其在GLUE和SUPER-GLUE上的效果进行报告,成为NLP进步的关键指标之一。

GLUE（General Language Understanding Evaluation）是一个用于评估自然语言处理（NLP）模型性能的基准测试集合。GLUE基准测试涵盖了多个任务，包括文本分类、语义相似度、自然语言推断等。下面是GLUE基准测试中包括的九个任务的详细解释：

1. **CoLA（Corpus of Linguistic Acceptability）:**
   
   - **任务：** 判断给定的句子是否在语法和语义上是可接受的。
   - **示例：** "He is playing the piano."（可接受） vs. "Him play piano."（不可接受）

2. **SST-2（Stanford Sentiment Treebank）:**
   
   - **任务：** 对给定的句子进行情感分类，判断情感是正面、负面还是中性的。
   - **示例：** "I love this product!"（正面） vs. "This is the worst movie ever."（负面）

3. **MRPC（Microsoft Research Paraphrase Corpus）:**
   
   - **任务：** 判断给定的两个句子是否是语义上相似的。
   - **示例：** "The cat is on the mat." vs. "The cat is lying on the mat."（相似）

4. **STS-B（Semantic Textual Similarity Benchmark）:**
   
   - **任务：** 对给定的两个句子进行语义相似度评分。
   - **示例：** "A man is playing a large flute." vs. "A man is playing a flute."（相似度得分）

5. **QQP（Quora Question Pairs）:**
   
   - **任务：** 判断给定的两个问题是否是语义上相似的。
   - **示例：** "How can I be a good geologist?" vs. "What should I do to be a great geologist?"（相似）

6. **MNLI（MultiNLI）:**
   
   - **任务：** 自然语言推断任务，给定一个前提句子和一个假设句子，判断假设在给定前提下是蕴含、矛盾还是中立关系。
   - **示例：** Premise: "The cat is sitting on the windowsill."， Hypothesis: "The cat is outside."（矛盾）

7. **QNLI（Question Natural Language Inference）:**
   
   - **任务：** 是MNLI任务的变体，专注于问题和短文本之间的推断关系。

8. **RTE（Recognizing Textual Entailment）:**
   
   - **任务：** 自然语言推断任务，判断给定的两个文本之间是否存在蕴涵关系。
   - **示例：** Text: "A soccer game with multiple males playing."， Hypothesis: "Some men are playing a sport."（蕴涵）

以上是GLUE基准测试中包括的九个任务，每个任务都有一组训练和测试数据，用于评估模型在不同自然语言处理任务上的性能。这些任务旨在涵盖多样的自然语言理解方面，从而全面评估模型的通用性。