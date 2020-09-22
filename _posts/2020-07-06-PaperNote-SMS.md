---
title: 【论文笔记】Sentence Mover’s Similarity: Automatic Evaluation for Multi-Sentence Texts Elizabeth
tags: 论文 TextSimilarity OptimalTransport RL NLP
---

## 概述

在翻译和摘要等文本生成任务中，为了节约人力和时间，我们需要一种自动评估机器生成文本的质量的方式。

* 本文提出了一种计算（多个句子组成的）文本相似度的方式Sentence Mover’s Similarity（SMS）。
* SMS可以用来判断生成文本和参考文本之间的语义距离，从而评估机器生成本文的质量。
* 在评估摘要文本上，基于SMS的方法与人类判断的相关性显著高于基于ROUGE的方法。
* SMS还可以用做强化学习训练生成模型时的奖励。
* 在文本摘要任务上，根据人类评估，使用SMS训练的生成模型相比使用Rouge-L训练的模型可以产生更高质量的摘要。

## 传统方式

* 传统的方式有
    * 摘要：ROUGE
    * 机器翻译：BLEU
    * 图像理解：METEOR、CIDER
* 基于单词匹配
* 缺点：
    * 无法捕获从参考文本中改写（reword）或重新排序（reorder）来的信息
    * 与人类判断的相关性弱

## Word Mover's Similarity

### 计算方式

Word Mover's Distance（WMD）是一种衡量两个序列之间距离的方式。序列可以是句子、段落等。

首先对两个序列A和B，计算每个序列的相对词频，即对每个词i：
$$
d_{A,i} = {count(i) \over |A|}
$$


这里|A|是序列A的总词数，d_{B, i}的定义是相似的。

然后定义两个词之间的距离为它们词嵌入向量的欧式距离：
$$
\Delta(i, j) = ||v_i - v_j||
$$
最后WMD就是下面这个线性规划问题的解（V是字典的大小，T是一个V×V的矩阵）：
$$
WMD(A, B) = \min_{T≥0}\Sigma_{i=1}^{V}\Sigma_{i=1}^{V}T_{i, j}\Delta(i, j)
$$
使得A中每个词流出的权重等于它在A中的权重：
$$
\forall i, \Sigma_{j=1}^VT_{i,j} = d_{A, i}
$$
且B中每个词流入的权重等于它在B中的权重：
$$
\forall j, \Sigma_{i=1}^VT_{i,j} = d_{B, i}
$$

Word Mover's Similarity（WMS）定义为：
$$
WMS(A, B) = exp(-WMD(A, B))
$$
WMS通过两份文本之间词的最小移动距离衡量它们的相似度。

### 存在的问题

* 随着文本长度的增长，计算成本上升很快（O(n^3logn)）
* 它是基于词袋的（BOW），当文本很长时，句子之间的关系会消失
* 仅仅衡量词之间的距离，没有考虑一组词（如一个句子）共同传递的信息，因此需要更高层次的文本表示（句子层次）

## Sentence Mover's Similarity Metrics

### Sentence Mover's Similarity

SMS类似WMS，但是将文本视为Bag of Sentence而不是Bag of Word。

句子向量为该句子的所有词向量的平均，句子之间的距离为句子向量间的欧氏距离。

文本A中的句子i的权重定义为：
$$
d_{A, i} = {|i| \over |A|}
$$
然后解和WMS相同的线性规划问题。

SMS通过两份文本之间句子的最小移动距离衡量它们的相似度。

### Sentence and Word Mover's Similarity

S+WMS，将文本视为Bag of Both Sentences & Words。
$$
d_{A, i} =
\begin{cases}
{count(i) \over 2|A|}, {\text{if i is a word}} \\
{|i| \over 2|A|}, {\text{if i is a sentence}}
\end{cases}
$$

## 实验结果

### Intrinsic Evaluation

* 评估机器生成文本，CNN/Daily Mail dataset
    * SMS与人类判断的相关度最好。基于句子的两个方式（SMS和S+WMS）都比ROUGE-L和WMS表现好
    * 词向量的类型对结果没有显著影响（GloVe/ELMo）

* 评估人类写作的文本，使用考试中一个摘要题目的学生回答作为数据集
    * S+WMS与人类判断的相关度最好。基于句子的两个方式（SMS和S+WMS）都比ROUGE-L和WMS表现好
    * 词向量的类型对结果没有显著影响（GloVe/ELMo）

### Extrinsic Evaluation

* 自动判断：

We evaluate the generated summaries from each model with ROUGE-L, WMS, SMS, and S+WMS in Table 4.

Across all metrics, the models trained using WMS and SMS metrics as the reward outperform models trained with ROUGE-L as the reward func- tion. S+WMS models lag behind ROUGE-L. The SMS model outperforms all other models across all metrics on the abstractive summarization task, consistent with SMS’s performance at evaluating summaries。

* 人类判断：

We asked human judges to evaluate the output of the mixed loss model trained with a ROUGE-L reward versus models trained with WMS, SMS, and S+WMS the reward. The results are shown in Table 6.

Human judges significantly prefer summaries produced by models optimized with WMS, SMS, and S+WMS over ROUGE-L. SMS and S+WMS were preferred over ROUGE-L more often than WMS was. There is no significant difference be- tween the evaluations of SMS and S+WMS.

### 存在的问题

* 准确性：更好的Encoder和计算句子嵌入向量方式

## 结论

* 本文提出了SMS和S+WMS两种衡量指标，用于自动评估由多个句子组成的文本与参考文本之间的语义相似度
* SMS和S+WMS通过引入句嵌入向量，显著改进了模型判断与人类判断的相关程度（相比ROUGE）
* 这两个衡量指标还可用于训练强化学习文本生成模型，使用SMS作为奖励生成的摘要比使用ROUGE-L生成的摘要质量更高



