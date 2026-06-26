# MOCOLNet: Modality-Contrastive Learning Network for Multimodal SA

> arXiv:2306.02345 | 2023

## 核心贡献

提出 MOCOLNet，一个基于模态对比学习的多模态情感分析网络，通过跨模态对比学习实现模态表示的对齐，无需手动特征工程即可自动学习模态间互补信息。

## 方法

构建模态对比编码器，使用正样本对（同一情感标签的不同模态表示）和负样本对（不同情感标签的模态表示）进行对比学习。引入模态感知的负采样策略，选择语义相近但标签不同的样本作为难负例，提升对比学习的有效性。

## 所属分类

[[Categories/Multimodal-Sentiment]]
