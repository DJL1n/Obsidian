# Hierarchical Mutual Information Maximization for Multimodal SA

> arXiv:2210.03476 | 2022

## 核心贡献

提出分层互信息最大化方法，在多模态情感分析中以信息论视角优化模态间信息流动，最大化联合表示与情感标签的互信息同时最小化各模态与联合表示的冗余。

## 方法

建立三层互信息目标：1）模态-标签互信息最大化；2）模态间互信息最大化（互补性）；3）联合表示-标签互信息最大化。使用信息瓶颈（Information Bottleneck）原理，通过变分下界估计互信息，使用对比散度（contrastive divergence）优化。
