# Dual Perception Fusion for Multimodal Sentiment Analysis

> arXiv:2208.07521 | 2022

## 核心贡献

提出双重感知融合（Dual Perception Fusion）机制，从"语义感知"和"情感感知"两个互补视角分别融合多模态信息，提升模型对模态间细微情感差异的敏感度。

## 方法

构建双分支融合架构：语义感知分支关注跨模态内容一致性（如视觉场景与文本描述的对齐），情感感知分支关注跨模态情感一致性（如面部表情与语音语调的情感匹配）。两分支通过交叉注意力交换信息，最终拼接作为分类器输入。

## 主题标签
#multimodal-fusion #dual-branch #semantic-emotion-decomposition

## 相关论文

- [[Bi-Bimodal-Fusion-Multimodal-SA.md]] — 两者都用双分支/双向设计，但分解维度不同
- [[Modality-Specific-Representations-Multimodal-SA.md]] — 都强调模态间差异建模
- [[Cross-Modal-Enhancement-Multimodal-SA.md]] — 都涉及模态间信息增强

## 所属分类
[[Categories/Multimodal-Sentiment]]
