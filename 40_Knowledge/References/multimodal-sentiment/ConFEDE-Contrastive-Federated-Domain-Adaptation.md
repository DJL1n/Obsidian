# ConFEDE: Contrastive Federated Domain Adaptation for Multimodal Sentiment Analysis

> arXiv:2305.12345 | 2023

## 核心贡献

提出 ConFEDE 框架，将对比学习与联邦学习结合用于跨域多模态情感分析，在不共享原始数据的前提下实现领域自适应，保护隐私同时提升跨域泛化能力。

## 方法

在联邦学习框架下，每个客户端使用本地多模态数据（文本-音频-视觉）训练，通过对比学习拉近同域正样本对、推远异域负样本对。全局聚合时仅上传模型参数，使用对抗性域对齐损失约束跨域表示一致性。

## 所属分类

[[Categories/Multimodal-Sentiment]]
