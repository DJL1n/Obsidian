# RGAT-ABSA: Relational Graph Attention Network for Aspect-Based Sentiment Analysis

> arXiv:2005.06421 | 2020

## 核心贡献

提出 RGAT-ABSA，将方面级情感分析建模为关系图上的表示学习任务，通过关系图注意力网络（RGAT）捕捉词语之间的句法和语义关系，实现更精确的方面-情感关联预测。

## 方法

构建依存句法图作为输入，每个节点为词元，边类型为依存关系。使用 RGAT 在图上传播消息，根据边的关系类型应用不同的注意力权重。针对目标方面词引入特殊标记（[CLS]），通过图注意力聚合其上下文信息后进行情感分类。

## 主题标签
#absa #graph-neural-network #dependency-parsing

## 相关论文

- [[SemEval-2014-TASK-4-ABSA.md]] — 在 SemEval 2014 基准上评测
- [[Aspect-Category-Opinion-Sentiment-Quadruple-Extraction.md]] — 都关注细粒度方面定位，RGAT 用图结构、四元组用关系分类
- [[ABSA-Survey-and-Benchmark.md]] — RGAT 是图神经方法在 ABSA 中的代表

## 所属分类
[[Categories/Multimodal-Sentiment]]
