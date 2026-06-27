# SemEval 2022 Task 6: Multimodal Emotion Detection

> arXiv:2203.05908 | 2022

## 核心贡献

SemEval 2022 Task 6 专注于多模态情感检测任务，提供包含文本、音频、视频的多模态社交媒体数据集，评估系统在跨模态情感识别上的性能，推动多模态情感分析基准标准化。

## 方法

参与者通常采用特征级或决策级融合策略，使用预训练模型（BERT、Wav2Vec2、VideoMAE）提取各模态特征后通过融合网络（如 LSTM、Transformer、MFN）联合建模。最佳系统多采用跨模态注意力和对比学习提升融合质量。

## 主题标签
#benchmark #semeval #multimodal-emotion

## 相关论文

- [[ABSA-Survey-and-Benchmark.md]] — 多模态情感 vs 方面级情感分析的基准对比
- [[SemEval-2014-TASK-4-ABSA.md]] — 从单模态 ABSA 到多模态情感检测的演进
- [[MOCOLNet-Modality-Contrastive-Learning.md]] — SemEval 最佳系统常用的对比学习方法

## 所属分类
[[Categories/Multimodal-Sentiment]]
