# Domain Adaptation for Sentiment Classification via Biographies and Bollywood

> arXiv:1608.03131 | 2016

## 核心贡献

探索领域自适应在情感分析中的应用，通过跨领域迁移学习解决目标领域标注数据稀缺问题。论文展示了从源领域（如 Yelp 评论）向目标领域（如亚马逊产品评论）迁移情感分类器的有效性。

## 方法

使用领域自适应技术，通过对比源域和目标域的特征分布，采用最大均值差异（MMD）或对抗性训练对齐跨领域表示。核心是将情感分析建模为领域自适应分类问题，利用源域的大量标注数据和目标域的少量无标注数据联合训练。

## 主题标签
#domain-adaptation #mmd #adversarial #classic

## 相关论文

- [[Domain-Adaptation-Sentiment.md]] — 同一领域的经典域自适应研究
- [[VAE-Adversarial-Domain.md]] — 对抗性域对齐的现代实现
- [[ConFEDE-Contrastive-Federated-Domain-Adaptation.md]] — 在联邦框架下做域自适应

## 所属分类
[[Categories/Multimodal-Sentiment]]
