# Sentiment Knowledge Enhanced Self-Supervised Learning for Multimodal SA

> arXiv:2302.04521 | 2023

## 核心贡献

提出情感知识增强的自监督学习方法，将外部情感知识（如 SenticNet、HowNet）作为辅助信号引入多模态情感分析的预训练阶段，提升模型在少样本和零样本场景下的表现。

## 方法

构建情感知识图谱作为先验，通过知识蒸馏将情感语义注入多模态编码器。设计自监督对比学习目标，正样本对为同情感类别的多模态样本，负样本对由知识图谱指导生成，确保语义有意义的负样本。
