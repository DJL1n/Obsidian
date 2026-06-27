# SemEval 2014 Task 4: Aspect Based Sentiment Analysis

> arXiv:1404.1658 | 2014

## 核心贡献

SemEval 2014 Task 4 首次大规模评测方面级情感分析（ABSA），提供 Restaurant 和 Laptop 两个领域的标注数据集，包含方面类别预测、方面情感预测和方面词三元组抽取三个子任务。

## 方法

参赛者主要采用基于规则和特征工程的方法：使用依存句法分析、POS 标注和词典方法提取方面词和情感词，结合 SVM、最大熵等传统分类器进行情感预测。该任务奠定了 ABSA 领域的基础评估框架。

## 主题标签
#absa #benchmark #semeval #foundation

## 相关论文

- [[ABSA-Survey-and-Benchmark.md]] — ABSA 综述，涵盖 SemEval 2014 在内的整个领域发展
- [[RGAT-ABSA-Relational-Graph-Attention.md]] — 在 SemEval 2014 数据集上评测的图注意力 ABSA 方法
- [[Aspect-Category-Opinion-Sentiment-Quadruple-Extraction.md]] — ABSA 的细粒度扩展——从二元组到四元组
- [[ChatGPT-ABSA-Limits-Study.md]] — 用 SemEval 2014/2015/2016 评测 ChatGPT 的 ABSA 能力
- [[EASE-Efficient-ABSA.md]] — 高效 ABSA 框架，同样面向 SemEval 基准

## 所属分类
[[Categories/Multimodal-Sentiment]]
