# Learning Transferable Visual Models From Natural Language Supervision (CLIP)

> arXiv:2103.00020 | 2021

## 核心贡献
用自然语言监督训练视觉模型，实现零样本图像分类和跨模态检索。

## 方法
CLIP通过对比学习在大规模图像-文本配对数据上训练视觉-语言联合表示空间。架构由两部分组成：基于ResNet或ViT的图像编码器，和基于Transformer的文本编码器。训练目标是最大化匹配图像-文本对的相似度（cosine similarity），同时最小化不匹配对的相似度，使用InfoNCE损失函数（对比损失）。模型在4亿张互联网爬取的图像-文本对上训练。核心能力：零样本分类——将类别名称作为文本提示输入，直接计算图像与各文本类别的相似度进行预测，无需微调；跨模态检索——在ImageNet zero-shot分类上达到90%+ResNet-50准确率；在商品分类、医学图像等未见过的任务上表现优异。CLIP证明了大规模弱监督学习可产生高度可迁移的视觉表示。

## 影响
CLIP开创了大规模视觉-语言预训练范式，被引超过5万次，是零样本学习和多模态AI的基石。直接启发了ALIGN、BLIP、Flamingo、BLIP-2等工作。

## 相关笔记
- [[Supervised-Contrastive]]
- [[Stable-Diffusion]]

## 所属分类

[[Categories/Vision-Language]]
