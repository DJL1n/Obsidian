# CLMLF: Cross-Modal Latent Multi-Level Fusion

> arXiv:2301.08945 | 2023

## 核心贡献

提出 CLMLF 框架，通过跨模态潜在空间的多层次融合解决模态异步和对齐问题，在无需严格时间对齐的多模态数据上实现高效的情感分析。

## 方法

在潜在空间中构建多层次融合塔：底层对齐模态间时间步，中层通过跨模态注意力捕捉语义互补，高层聚合多层次的融合表示。使用解耦的损失函数分别优化模态内一致性和跨模态互补性。

## 主题标签
#multimodal-fusion #latent-space #multi-level #temporal-alignment

## 相关论文

- [[Layer-wise-Fusion-Multimodal-Sentiment.md]] — 都强调多层次融合，CLMLF 额外处理时间异步
- [[Multi-Level-Correlation-Mining-Multimodal-SA.md]] — 都涉及多粒度建模
- [[TEASEL.md]] — 同样关注时序对齐的语音-文本融合

## 所属分类
[[Categories/Multimodal-Sentiment]]
