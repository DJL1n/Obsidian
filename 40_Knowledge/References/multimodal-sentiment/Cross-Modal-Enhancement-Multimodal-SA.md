# Cross-Modal Enhancement for Multimodal Sentiment Analysis

> arXiv:2206.08117 | 2022

## 核心贡献

提出跨模态增强机制，通过生成式模块补全缺失或弱信号的模态信息，增强主模态的情感表示，在模态不完整场景下保持稳定的情感分析性能。

## 方法

使用跨模态 Transformer 编码器-解码器，以强模态（如文本）为条件生成弱模态的补充表示。增强后的表示与原始表示拼接后送入融合模块。训练时采用模态随机丢弃（modal dropout）模拟模态缺失，提升鲁棒性。

## 所属分类

[[Categories/Multimodal-Sentiment]]
