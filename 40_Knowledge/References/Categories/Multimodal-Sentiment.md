# Multimodal-Sentiment

> 多模态情感分析

---

## 研究脉络

见 [[ResearchThreads/multimodal-sentiment-analysis]]

## 论文分类索引

### 融合方法 (#multimodal-fusion)

| 论文 | 方法 | 年份 |
|------|------|------|
| [[multimodal-sentiment/Layer-wise-Fusion-Multimodal-Sentiment]] | 逐层跨模态注意力 | 2021 |
| [[multimodal-sentiment/Image-Text-Interaction-Sentiment-Analysis]] | 两模态 ViT-BERT 交叉注意力 | 2021 |
| [[multimodal-sentiment/Modality-Specific-Representations-Multimodal-SA]] | 模态特定表示 + 解耦注意力 | 2021 |
| [[multimodal-sentiment/Bi-Bimodal-Fusion-Multimodal-SA]] | 两两模态双向交叉注意力 | 2022 |
| [[multimodal-sentiment/Dual-Perception-Fusion-Multimodal-SA]] | 语义/情感双分支融合 | 2022 |
| [[multimodal-sentiment/Cross-Modal-Enhancement-Multimodal-SA]] | 生成式跨模态增强 + modal dropout | 2022 |
| [[multimodal-sentiment/Hyper-modality-Fusion-Multimodal-Sentiment]] | 超网络动态权重 | 2022 |
| [[multimodal-sentiment/Hierarchical-Mutual-Information-Multimodal-SA]] | 分层互信息最大化 | 2022 |
| [[multimodal-sentiment/Condense-Attention-Fusion-Multimodal-SA]] | 注意力蒸馏压缩 | 2022 |
| [[multimodal-sentiment/Multi-Level-Correlation-Mining-Multimodal-SA]] | 细/中/粗三级相关性 | 2023 |
| [[multimodal-sentiment/CLMLF-Cross-Modal-Latent-Multi-Level-Fusion]] | 潜在空间多塔 + 异步对齐 | 2023 |

### 自监督/对比学习 (#self-supervised, #contrastive-learning)

| 论文 | 方法 | 年份 |
|------|------|------|
| [[multimodal-sentiment/Self-Supervised-MultiModal]] | 三任务预训练（掩码+对比+时序） | 2023 |
| [[multimodal-sentiment/Sentiment-Knowledge-Enhanced-SSL]] | 自监督 + 情感知识增强 | 2023 |
| [[multimodal-sentiment/MOCOLNet-Modality-Contrastive-Learning]] | 模态对比学习 + 难负例 | 2023 |
| [[multimodal-sentiment/Hybrid-Contrastive-Tri-Modal-SA]] | 实例级+模态级双粒度对比 | 2023 |

### 方面级情感分析 ABSA (#absa)

| 论文 | 方法 | 年份 |
|------|------|------|
| [[multimodal-sentiment/SemEval-2014-TASK-4-ABSA]] | ABSA 奠基性评测 | 2014 |
| [[multimodal-sentiment/RGAT-ABSA-Relational-Graph-Attention]] | 图注意力 + 依存句法 | 2020 |
| [[multimodal-sentiment/Aspect-Category-Opinion-Sentiment-Quadruple-Extraction]] | 四元组抽取 | 2020 |
| [[multimodal-sentiment/EASE-Efficient-ABSA]] | 高效序列标注 + 蒸馏 | 2022 |
| [[multimodal-sentiment/ChatGPT-ABSA-Limits-Study]] | LLM zero-shot ABSA 评估 | 2023 |
| [[multimodal-sentiment/ABSA-Survey-and-Benchmark]] | 全面综述 + 新基准 | 2023 |

### 领域自适应 (#domain-adaptation)

| 论文 | 方法 | 年份 |
|------|------|------|
| [[multimodal-sentiment/Domain-Adaptation-Sentiment]] | 特征重加权 | 2008 |
| [[multimodal-sentiment/Domain-Adaptation-Sentiment-Biographies-Bollywood]] | MMD + 对抗训练 | 2016 |
| [[multimodal-sentiment/VAE-Adversarial-Domain]] | VAE + 对抗性域对齐 | 2023 |

### 联邦学习 (#federated-learning)

| 论文 | 方法 | 年份 |
|------|------|------|
| [[multimodal-sentiment/Federated-Contrastive-Multimodal-SA]] | 联邦 + 对比学习 | 2023 |
| [[multimodal-sentiment/ConFEDE-Contrastive-Federated-Domain-Adaptation]] | 联邦 + 对比 + 域自适应 | 2023 |

### 架构创新与特殊方法

| 论文 | 方法 | 年份 |
|------|------|------|
| [[multimodal-sentiment/TEASEL]] | Speech-prefix LM | 2022 |
| [[multimodal-sentiment/UniMSE-Unified-Multimodal-Sentiment-Encoder]] | 统一多模态编码器 | 2023 |
| [[multimodal-sentiment/SKEAFN]] | SenticNet 知识增强注意力 | 2022 |
| [[multimodal-sentiment/Quantum-Inspired-Sentiment-Analysis]] | 张量网络量子启发 | 2023 |
| [[multimodal-sentiment/Counterfactual-VQA]] | 反事实样本去偏见 | 2021 |
| [[multimodal-sentiment/SemEval-2022-TASK-6-Multimodal-Emotion]] | 多模态情感检测基准 | 2022 |
