# Hybrid Contrastive Learning for Tri-Modal Sentiment Analysis

> arXiv:2303.07829 | 2023

## 核心贡献

提出混合对比学习框架，在三模态（文本、音频、视觉）情感分析中同时学习模态内一致性和模态间互补性，通过多粒度对比损失提升跨模态表示质量。

## 方法

设计两种对比目标：1）实例级对比——同一样本的多模态表示应相近，不同样本应相远；2）模态级对比——同一情感类别下不同模态的表示应互相拉近。使用 InfoNCE 损失结合两种对比信号，通过温度参数控制对比粒度。

## 所属分类

[[Categories/Multimodal-Sentiment]]
