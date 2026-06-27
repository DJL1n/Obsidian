# Multi-Level Correlation Mining for Multimodal Sentiment Analysis

> arXiv:2301.04521 | 2023

## 核心贡献

提出多层次相关性挖掘方法，在多模态情感分析中同时捕获局部（token/帧级）和全局（序列级）跨模态相关性，解决传统方法只关注单一粒度对齐的局限。

## 方法

设计三级相关性建模：1）细粒度——使用交叉注意力对齐文本词元和视觉/音频帧；2）中粒度——通过模态交互 Transformer 捕获跨模态语义块的相关性；3）粗粒度——使用全局池化和模态一致性损失对齐整体情感倾向。

## 主题标签
#multimodal-fusion #multi-granularity #correlation-mining

## 相关论文

- [[Layer-wise-Fusion-Multimodal-Sentiment.md]] — 两者都强调多粒度跨模态交互，前者逐层、本文按细/中/粗三级
- [[MOCOLNet-Modality-Contrastive-Learning.md]] — 都关注模态间相关性，但 MOCOLNet 用对比学习而非注意力
- [[Hybrid-Contrastive-Tri-Modal-SA.md]] — 同样涉及多粒度对比/相关性建模

## 所属分类
[[Categories/Multimodal-Sentiment]]
