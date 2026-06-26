# VAE-Based Adversarial Domain Transfer for Video-Level Sentiment Analysis

> arXiv:2302.08934 | 2023

## 核心贡献
用 VAE + 对抗性训练实现跨数据集的视频级情感分析零样本迁移。

## 方法
VAE 编码器将多模态输入映射到域不变潜在空间，域判别器通过对抗性梯度反转约束潜在表示无法区分源域和目标域。重构损失确保模态信息完整性，KL 散度约束潜在分布。在 Yelp→IMDb 和 MovieReview→Tweet 迁移场景上超越传统领域自适应方法。

## 所属分类

[[Categories/Multimodal-Sentiment]]
