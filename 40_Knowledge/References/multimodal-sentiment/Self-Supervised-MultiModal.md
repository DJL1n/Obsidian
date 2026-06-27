# Self-Supervised MultiModal Versatile Networks

> arXiv:2305.14523 | 2023

## 核心贡献
面向多模态情感分析的自监督预训练框架，三个预训练任务覆盖模态掩码、跨模态对比、时序预测。

## 方法
设计三任务预训练：1）随机掩码一个模态特征，让模型从其他模态预测；2）跨模态对比学习拉近同一样本不同模态的表示；3）时序自回归预测下一帧多模态特征。预训练后在 Mosei/Mosiad 上微调，少样本场景（10%标注）提升 8-12%。

## 主题标签
#self-supervised #pretraining #modal-masking #few-shot

## 相关论文

- [[Cross-Modal-Enhancement-Multimodal-SA.md]] — 同样用模态掩码，但前者是训练策略、本文是预训练任务
- [[UniMSE-Unified-Multimodal-Sentiment-Encoder.md]] — 两者都是统一的多模态预训练框架
- [[Sentiment-Knowledge-Enhanced-SSL.md]] — 同样是自监督预训练，但后者加入情感知识先验
- [[MOCOLNet-Modality-Contrastive-Learning.md]] — 都包含跨模态对比学习任务

## 所属分类
[[Categories/Multimodal-Sentiment]]
