# Aspect-Category-Opinion-Sentiment Quadruple Extraction

> arXiv:2010.03942 | 2020

## 核心贡献

提出细粒度 ABSA 中的四元组抽取任务（Aspect-Category-Opinion-Sentiment Quadruple Extraction），将传统的二元组（方面-情感）扩展到四元组，同时预测方面词、所属类别、意见词和情感极性。该框架提供了更丰富的细粒度情感分析能力。

## 方法

将四元组抽取建模为关系分类任务，使用基于预训练语言模型（BERT/RoBERTa）的编码器提取上下文嵌入，通过注意力机制匹配方面词、类别、意见词三元组，最后分类情感极性。采用解码器设计将四元组生成转化为序列标注问题。

## 所属分类

[[Categories/Multimodal-Sentiment]]
