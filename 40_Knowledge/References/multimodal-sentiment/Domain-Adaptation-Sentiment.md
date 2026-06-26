# Domain Adaptation for Sentiment Classification (BoBiBoBle)

> arXiv:0812.3850 | 2008

## 核心贡献
经典领域自适应情感分类论文，从电影评论域迁移到生物、宝莱坞、音响、搅拌机四个目标域。

## 方法
使用特征选择 + 重加权策略，源域训练数据按目标域特征分布重加权。核心洞察是不同领域的情感词汇分布差异巨大，直接用源域分类器迁移效果极差。通过调整特征权重使源域训练分布逼近目标域分布，在多个跨域场景上超越 bagged baselines。

## 所属分类

[[Categories/Multimodal-Sentiment]]
