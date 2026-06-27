# SKEAFN: Sentiment Knowledge Enhanced Attention Fusion Network

> arXiv:2204.07856 | 2022

## 核心贡献
将 SenticNet 情感知识库注入多模态注意力融合，用知识引导的注意力权重提升跨模态情感分析精度。

## 方法
把 SenticNet 中预计算的情感原型向量作为查询模板，与输入多模态特征做交叉注意力。模态特征与知识原型的匹配度决定注意力权重，使模型优先关注有明确情感语义的模态片段。使用知识蒸馏将 SenticNet 的语义结构迁移到多模态编码器，在 Mosei/Multimodal 数据集上超越 baselines 2-3 个百分点。

## 主题标签
#knowledge-enhanced #senticnet #attention-fusion

## 相关论文

- [[Sentiment-Knowledge-Enhanced-SSL.md]] — 都利用 SenticNet，SKEAFN 注入注意力、后者注入预训练
- [[Hierarchical-Mutual-Information-Multimodal-SA.md]] — 都用先验知识约束融合过程

## 所属分类
[[Categories/Multimodal-Sentiment]]
