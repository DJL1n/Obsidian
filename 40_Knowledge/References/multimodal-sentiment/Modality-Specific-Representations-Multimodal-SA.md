# Modality-Specific Representations for Multimodal Sentiment Analysis

> arXiv:2109.05684 | 2021

## 核心贡献

提出模态特定表示学习，强调在多模态情感分析中保留各模态的独特信息而非过早融合，通过模态感知的表示空间提升模型对模态特有情感线索的敏感性。

## 方法

为每个模态设计独立的编码器分支，提取模态特定特征后使用解耦注意力（disentangled attention）进行选择性融合。引入模态不变性约束和模态特异性约束，确保共享表示中保留跨模态共性而独有信息不被丢失。
