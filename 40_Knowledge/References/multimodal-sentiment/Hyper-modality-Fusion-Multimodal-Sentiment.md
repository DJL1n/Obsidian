# Hyper-modality Fusion for Multimodal Sentiment Analysis

> arXiv:2201.11637 | 2022

## 核心贡献

提出超模态（hyper-modality）融合机制，将多模态特征投影到统一的超空间中进行交互，通过可学习的超参数自动调节各模态在融合过程中的贡献权重，实现动态模态重要性感知。

## 方法

设计超网络（hypernetwork）生成主融合网络的权重，根据输入样本的模态特征分布动态调整融合策略。在超空间中对齐文本、音频、视觉模态的表示，使用自注意力机制捕捉模态间高阶关系，实现比静态融合更灵活的跨模态交互。

## 主题标签
#multimodal-fusion #hypernetwork #dynamic-weight

## 相关论文

- [[Condense-Attention-Fusion-Multimodal-SA.md]] — 都关注融合效率优化，前者动态加权、后者注意力蒸馏
- [[Modality-Specific-Representations-Multimodal-SA.md]] — 都涉及模态重要性感知
- [[Quantum-Inspired-Sentiment-Analysis.md]] — 都用数学结构建模高阶模态关联

## 所属分类
[[Categories/Multimodal-Sentiment]]
