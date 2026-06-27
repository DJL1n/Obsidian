# Layer-wise Fusion for Multimodal Sentiment Analysis

> arXiv:2104.07056 | 2021

## 核心贡献

提出层次化融合机制，在多模态情感分析中实现从底层特征到高层语义的逐层跨模态交互，替代传统的晚期融合（late fusion），充分捕捉模态间深层相关性。

## 方法

在每一网络层引入跨模态注意力模块，让文本、音频、视觉特征在编码的每一个阶段进行交互。使用门控机制控制每层融合程度，浅层侧重模态内特征提取，深层侧重跨模态语义对齐，最终通过多层融合特征进行情感分类。

## 主题标签
#multimodal-fusion #cross-attention #hierarchical-fusion

## 相关论文

- [[Multi-Level-Correlation-Mining-Multimodal-SA.md]] — 同样关注多层次/多粒度融合，但后者更强调相关性挖掘而非逐层交互
- [[Bi-Bimodal-Fusion-Multimodal-SA.md]] — 同样是跨模态注意力融合，但 Bi-Bimodal 专注于两两模态的双向融合
- [[Dual-Perception-Fusion-Multimodal-SA.md]] — 同样是多视角融合，但后者从语义/情感双视角切入
- [[CLMLF-Cross-Modal-Latent-Multi-Level-Fusion.md]] — 都在潜在空间做多层次融合，CLMLF 额外解决异步对齐问题

## 所属分类
[[Categories/Multimodal-Sentiment]]
