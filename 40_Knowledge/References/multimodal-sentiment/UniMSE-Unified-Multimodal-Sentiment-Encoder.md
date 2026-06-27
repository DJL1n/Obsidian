# UniMSE: Unified Multimodal Sentiment Encoder

> arXiv:2305.08964 | 2023

## 核心贡献

提出 UniMSE，一个统一的多模态情感编码器，支持文本、音频、视觉三种模态的统一编码，实现跨模态情感表示的零样本迁移和模态缺失鲁棒性。

## 方法

设计统一的 Transformer 架构，通过模态特定 token 区分不同模态输入。在预训练阶段使用模态掩码重建（masked modality reconstruction）和跨模态对比学习两个自监督任务，使模型学会模态间互补和冗余信息建模。

## 主题标签
#architecture #unified-encoder #zero-shot #modal-masking #pretraining

## 相关论文

- [[Self-Supervised-MultiModal.md]] — 两者都是统一的多模态预训练框架
- [[Cross-Modal-Enhancement-Multimodal-SA.md]] — 都处理模态缺失，UniMSE 靠预训练鲁棒性、后者靠生成式补全
- [[TEASEL.md]] — 统一编码 vs prefix LM 架构对比

## 所属分类
[[Categories/Multimodal-Sentiment]]
