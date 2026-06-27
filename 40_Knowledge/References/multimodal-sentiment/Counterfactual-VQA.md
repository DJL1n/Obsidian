# Counterfactual Samples for Robust Visual Question Answering

> arXiv:2106.14537 | 2021

## 核心贡献
用反事实样本生成提升 VQA 模型鲁棒性，消除语言偏见和世界知识偏见。

## 方法
通过图像编辑和文本改写生成反事实样本：替换图像中的关键物体或改写问题中的关键词。模型在真实样本和反事实样本上同时训练，迫使它学习真正的视觉-语言关联而非语言捷径。在 VQA-v2 和 GQA 上显著提升对抗测试集准确率。

## 主题标签
#robustness #counterfactual #vqa #bias-removal #vision-language

## 相关论文

- [[Image-Text-Interaction-Sentiment-Analysis.md]] — 同样研究视觉-语言交互，Counterfactual 关注去偏见
- [[Cross-Modal-Enhancement-Multimodal-SA.md]] — 都用数据增强提升鲁棒性

## 所属分类
[[Categories/Multimodal-Sentiment]]
