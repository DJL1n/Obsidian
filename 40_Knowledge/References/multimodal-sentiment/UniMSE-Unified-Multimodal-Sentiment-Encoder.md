# UniMSE: Unified Multimodal Sentiment Encoder

> arXiv:2305.08964 | 2023

## 核心贡献

提出 UniMSE，一个统一的多模态情感编码器，支持文本、音频、视觉三种模态的统一编码，实现跨模态情感表示的零样本迁移和模态缺失鲁棒性。

## 方法

设计统一的 Transformer 架构，通过模态特定 token 区分不同模态输入。在预训练阶段使用模态掩码重建（masked modality reconstruction）和跨模态对比学习两个自监督任务，使模型学会模态间互补和冗余信息建模。

## 所属分类

[[Categories/Multimodal-Sentiment]]
