# Exploring ChatGPT for Aspect-Based Sentiment Analysis: A Comprehensive Study

> arXiv:2304.04601 | 2023

## 核心贡献

系统评估 ChatGPT 在方面级情感分析（ABSA）上的能力与局限，发现 LLM 在 zero-shot ABSA 上表现尚可但远未达到 SOTA，在复杂场景（如多方面、隐含情感、反语）中显著退步。

## 方法

在 SemEval 2014/2015/2016 ABSA 基准上评测 ChatGPT 的 zero-shot 和 few-shot 表现，与 BERT 基线、专用 ABSA 模型对比。分析失败案例，发现 ChatGPT 的主要局限包括：缺乏细粒度方面定位能力、对领域特定术语敏感度低、无法区分共现的多个方面词。
