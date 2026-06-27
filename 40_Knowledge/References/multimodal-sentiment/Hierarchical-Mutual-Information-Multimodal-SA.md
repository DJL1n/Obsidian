# Hierarchical Mutual Information Maximization for Multimodal SA

> arXiv:2210.03476 | 2022

## 核心贡献

提出分层互信息最大化方法，在多模态情感分析中以信息论视角优化模态间信息流动，最大化联合表示与情感标签的互信息同时最小化各模态与联合表示的冗余。

## 方法

建立三层互信息目标：1）模态-标签互信息最大化；2）模态间互信息最大化（互补性）；3）联合表示-标签互信息最大化。使用信息瓶颈（Information Bottleneck）原理，通过变分下界估计互信息，使用对比散度（contrastive divergence）优化。

## 主题标签
#multimodal-fusion #mutual-information #information-bottleneck #theoretical

## 相关论文

- [[Modality-Specific-Representations-Multimodal-SA.md]] — 两者都用信息论约束模态间关系
- [[Hybrid-Contrastive-Tri-Modal-SA.md]] — 都涉及模态间信息最大化，但后者用对比学习实现
- [[MOCOLNet-Modality-Contrastive-Learning.md]] — 对比学习是互信息估计的实践方法

## 所属分类
[[Categories/Multimodal-Sentiment]]
