# Bi-Bimodal Fusion for Multimodal Sentiment Analysis

> arXiv:2205.06789 | 2022

## 核心贡献

提出双向双模态融合（Bi-Bimodal Fusion）机制，在视频情感分析中实现两两模态（文本-视觉、文本-音频、视觉-音频）之间的双向交叉注意力融合，充分捕捉模态间互信息。

## 方法

对三模态输入，分别构建三个双模态融合模块，每个模块使用交叉注意力让两个模态互相作为 query 和 key/value 进行双向信息交换。三个融合结果通过门控网络聚合，门控权重由模态置信度动态计算。

## 主题标签
#multimodal-fusion #cross-attention #pairwise-fusion

## 相关论文

- [[Layer-wise-Fusion-Multimodal-Sentiment.md]] — 同样用跨模态注意力，但前者逐层融合、本文按两两模态组合
- [[Dual-Perception-Fusion-Multimodal-SA.md]] — 两者都有双分支设计思路
- [[Cross-Modal-Enhancement-Multimodal-SA.md]] — 都关注模态间互补信息利用

## 所属分类
[[Categories/Multimodal-Sentiment]]
