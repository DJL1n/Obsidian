# Supervised Contrastive Learning

> arXiv:2004.11362 | 2020

## 核心贡献
提出监督对比损失，将标签信息融入对比学习，统一监督和无监督表征学习。

## 方法
论文将对比学习从自监督（基于数据增强构建正负样本对）扩展到监督设置。核心思想：同一类别的样本在特征空间中应相互靠近，不同类别的样本应相互远离。监督对比损失（Supervised Contrastive Loss）对每个样本i，最大化其同类别正样本j的相似度，同时最小化与所有负样本（不同类别）的相似度。损失函数为：L_i = -Σ_{j∈正样本} log(exp(sim(h_i,h_j)/τ) / Σ_{k∈同批次,k≠i} exp(sim(h_i,h_k)/τ))。与无监督对比学习（SimCLR、MoCo）不同，监督对比学习利用标签信息构建正样本对，无需大量数据增强。论文在ImageNet分类和下游少样本学习任务上证明：监督对比学习显著优于交叉熵损失训练的ResNet，尤其在特征提取和迁移学习场景中。

## 影响
该工作是连接对比学习和传统分类的桥梁，被广泛引用，启发了许多对比学习变体。为下游任务的表征学习和微调提供了更好的特征空间基础。

## 相关笔记
- [[CLIP]]
- [[SimCLR-Self-Supervised-Learning]]
- [[Denoising-Diffusion-Probabilistic-Models-DDPM]]
