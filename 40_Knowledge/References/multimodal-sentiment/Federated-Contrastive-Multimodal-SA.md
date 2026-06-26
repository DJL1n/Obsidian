# Federated Contrastive Learning for Multimodal Sentiment Analysis

> arXiv:2304.01256 | 2023

## 核心贡献

提出联邦对比学习框架用于多模态情感分析，在保护各参与方数据隐私的前提下，通过全局对比学习实现跨数据集的多模态表示迁移和领域泛化。

## 方法

在联邦学习架构下，各客户端本地训练多模态编码器，上传全局对比锚点（contrastive anchors）而非原始数据。服务端聚合各客户端的对比信号，构建全局对比损失。使用自编码器重构损失约束本地表示的完整性，防止信息泄露。

## 所属分类

[[Categories/Multimodal-Sentiment]]
