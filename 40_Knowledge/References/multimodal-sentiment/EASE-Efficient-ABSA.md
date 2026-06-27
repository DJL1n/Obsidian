# EASE: Efficient Aspect-based Sentiment Extraction

> arXiv:2208.06712 | 2022

## 核心贡献

提出 EASE 框架，将方面级情感抽取（ABSAE）建模为高效的序列标注任务，在保持高精度的同时显著降低计算开销，适用于大规模实时情感分析场景。

## 方法

使用轻量级 Transformer（如 DistilBERT）作为编码器，将方面词抽取和情感分类统一为 token-level 标签预测任务。引入 span-aware 注意力机制和知识蒸馏，从大型教师模型（如 RoBERTa-large）迁移知识到紧凑学生模型。

## 主题标签
#absa #efficiency #knowledge-distillation

## 相关论文

- [[ChatGPT-ABSA-Limits-Study.md]] — 都涉及 ABSA 的效率-精度权衡
- [[Condense-Attention-Fusion-Multimodal-SA.md]] — 同样用知识蒸馏压缩模型
- [[ABSA-Survey-and-Benchmark.md]] — EASE 是高效 ABSA 的代表方法

## 所属分类
[[Categories/Multimodal-Sentiment]]
