# Image-Text Interaction for Sentiment Analysis

> arXiv:2110.06789 | 2021

## 核心贡献

研究图像-文本交互在社交媒体情感分析中的作用，提出细粒度跨模态交互机制，捕捉图片内容（场景、对象、文字）与配文之间的互补和冲突情感信号。

## 方法

使用 ViT-BERT 架构，视觉编码器（ViT）和文本编码器（BERT）并行提取特征后通过交叉注意力层进行交互。引入视觉-文本对齐损失（vision-text alignment loss）约束跨模态语义一致性，并在社交媒体数据集（如 Twitter 图像帖文）上验证。

## 主题标签
#vision-language #social-media #cross-attention #two-modal

## 相关论文

- [[TEASEL.md]] — 同样关注两种模态的自然融合方式
- [[Counterfactual-VQA.md]] — 同样研究视觉-语言交互，但后者是 VQA 任务
- [[Cross-Modal-Enhancement-Multimodal-SA.md]] — 都关注跨模态信息互补

## 所属分类
[[Categories/Multimodal-Sentiment]]
