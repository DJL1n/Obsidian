# Condense Attention Fusion for Multimodal Sentiment Analysis

> arXiv:2209.03456 | 2022

## 核心贡献

提出压缩注意力融合（Condense Attention Fusion），通过注意力蒸馏技术将复杂的跨模态注意力矩阵压缩为紧凑的融合表示，在保持融合质量的同时大幅降低计算复杂度。

## 方法

使用教师-学生蒸馏框架：教师模型为完整的多头交叉注意力融合，学生模型为压缩的单头注意力。蒸馏目标包括：注意力矩阵分布对齐（KL 散度）、融合表示匹配（MSE）、分类一致性（交叉熵）。压缩后参数量减少 60% 以上。

## 主题标签
#multimodal-fusion #attention-distillation #model-compression #efficiency

## 相关论文

- [[Hyper-modality-Fusion-Multimodal-Sentiment.md]] — 都优化融合计算效率
- [[EASE-Efficient-ABSA.md]] — 同样用知识蒸馏压缩模型
- [[CLMLF-Cross-Modal-Latent-Multi-Level-Fusion.md]] — 都涉及融合架构设计优化

## 所属分类
[[Categories/Multimodal-Sentiment]]
