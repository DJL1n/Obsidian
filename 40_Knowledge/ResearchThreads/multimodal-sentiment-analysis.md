# 多模态情感分析研究脉络

> 最后更新：2026-06-27

---

## 一、领域全景

多模态情感分析（Multimodal Sentiment Analysis, MSA）核心任务：融合文本、音频、视觉等多种模态信息，判断内容的情感倾向。32 篇论文可归纳为 **6 大主题方向**：

```
融合方法 (11) ── 核心主线：如何将多模态信息有效地结合起来
自监督/对比学习 (4) ── 预训练范式：减少对标注数据的依赖
ABSA (6) ── 细粒度分析：从粗粒度情感到方面级情感
领域自适应 (3) ── 跨域泛化：模型如何适应新领域
联邦学习 (2) ── 隐私保护：数据不出本地的协同学习
架构创新 (4) ── 新范式：prefix LM、统一编码器、量子启发等
```

---

## 二、核心主线：融合方法演进

融合是多模态情感分析最核心的技术挑战。32 篇中有 11 篇围绕融合展开，按时间/方法演进可以分为几个阶段：

### 2.1 早期融合范式（2021）

- [[Image-Text-Interaction-Sentiment-Analysis]] — 两模态（视觉-文本）交叉注意力，ViT-BERT 架构
- [[Modality-Specific-Representations-Multimodal-SA]] — 模态特定表示 + 解耦注意力，反对过早融合

**关键洞察**：两模态 vs 三模态；早期融合 vs 晚期融合的权衡

### 2.2 多层次/多粒度融合（2021-2023）

- [[Layer-wise-Fusion-Multimodal-Sentiment]] — 逐层跨模态交互（2021）
- [[Bi-Bimodal-Fusion-Multimodal-SA]] — 两两模态双向融合（2022）
- [[Dual-Perception-Fusion-Multimodal-SA]] — 语义/情感双分支（2022）
- [[Multi-Level-Correlation-Mining-Multimodal-SA]] — 细/中/粗三级相关性（2023）
- [[CLMLF-Cross-Modal-Latent-Multi-Level-Fusion]] — 潜在空间多塔 + 异步对齐（2023）

**演进趋势**：从单一注意力 → 多视角并行 → 多粒度分解 → 潜在空间建模

### 2.3 融合效率优化（2022-2023）

- [[Hyper-modality-Fusion-Multimodal-Sentiment]] — 超网络动态加权（2022）
- [[Condense-Attention-Fusion-Multimodal-SA]] — 注意力蒸馏压缩（2022）
- [[Hierarchical-Mutual-Information-Multimodal-SA]] — 信息论约束融合（2022）

**关键问题**：融合越复杂越好吗？如何在精度和效率之间平衡

### 2.4 模态缺失鲁棒性

- [[Cross-Modal-Enhancement-Multimodal-SA]] — 生成式补全 + modal dropout（2022）
- [[UniMSE-Unified-Multimodal-Sentiment-Encoder]] — 统一编码器 + 模态掩码预训练（2023）

---

## 三、自监督/对比学习方向

从监督学习到自监督预训练，减少对标注数据的依赖：

- [[Self-Supervised-MultiModal]] — 三任务预训练框架：模态掩码 + 跨模态对比 + 时序预测（2023）
- [[Sentiment-Knowledge-Enhanced-SSL]] — 自监督 + 情感知识增强（2023）
- [[MOCOLNet-Modality-Contrastive-Learning]] — 模态对比学习 + 难负例采样（2023）
- [[Hybrid-Contrastive-Tri-Modal-SA]] — 实例级 + 模态级双粒度对比（2023）

**关键洞察**：对比学习天然适合多模态——同一语义的不同模态表达就是正样本对

---

## 四、方面级情感分析（ABSA）

从粗粒度情感分类到细粒度方面级分析：

### 4.1 基础与基准
- [[SemEval-2014-TASK-4-ABSA]] — ABSA 领域奠基性评测（2014）
- [[ABSA-Survey-and-Benchmark]] — 全面综述 + 新基准（2023）

### 4.2 方法演进
- [[RGAT-ABSA-Relational-Graph-Attention]] — 图注意力 + 依存句法（2020）
- [[Aspect-Category-Opinion-Sentiment-Quadruple-Extraction]] — 四元组抽取（2020）
- [[EASE-Efficient-ABSA]] — 高效序列标注 + 蒸馏（2022）

### 4.3 LLM 时代的 ABSA
- [[ChatGPT-ABSA-Limits-Study]] — LLM zero-shot ABSA 能力评估（2023）

**演进路线**：规则/特征工程 → 深度学习 → 图结构建模 → 端到端抽取 → LLM 时代

---

## 五、领域自适应 + 联邦学习

解决数据分布偏移和隐私保护问题：

### 5.1 领域自适应
- [[Domain-Adaptation-Sentiment]] — 经典：特征重加权（2008）
- [[Domain-Adaptation-Sentiment-Biographies-Bollywood]] — MMD + 对抗训练（2016）
- [[VAE-Adversarial-Domain]] — VAE + 对抗性域对齐（2023）

### 5.2 联邦学习
- [[Federated-Contrastive-Multimodal-SA]] — 联邦 + 对比学习（2023）
- [[ConFEDE-Contrastive-Federated-Domain-Adaptation]] — 联邦 + 对比 + 域自适应三重组合（2023）

**趋势**：单域 → 跨域自适应 → 联邦协同 → 隐私保护 + 跨域 + 对比学习

---

## 六、架构创新

跳出传统融合范式的探索：

- [[TEASEL]] — Speech-prefix LM：语音特征作为 prefix tokens（2022）
- [[Quantum-Inspired-Sentiment-Analysis]] — 张量网络建模高阶关联（2023）
- [[Counterfactual-VQA]] — 反事实样本去偏见（2021）
- [[SKEAFN]] — 情感知识增强的注意力融合（2022）

---

## 七、关键问题与开放方向

### 已解决的问题
1. **基础融合** — 跨模态注意力已成标配
2. **模态缺失** — modal dropout + 预训练鲁棒性
3. **效率优化** — 蒸馏 + 压缩

### 仍在探索的问题
1. **多粒度对齐** — 细/中/粗三级的最佳粒度组合尚无定论
2. **自监督预训练** — 对比学习 vs 模态掩码，哪个更有效
3. **ABSA → MABSA** — 多模态方面级情感分析仍有很大空间
4. **联邦 + 对比 + 自适应** — 三者的统一框架（ConFEDE 起步）
5. **知识增强** — SenticNet/HowNet 如何更好地注入多模态模型

### 潜在研究方向
- **统一多模态基础模型**：类比 LLM，是否有 MDM（Multimodal Distillation Model）？
- **因果多模态融合**：反事实方法能否解决模态偏见？
- **实时多模态情感分析**：流式场景下的低延迟融合

---

## 八、论文之间的方法论继承关系

```
                    SemEval 2014 (ABSA 基础)
                           │
              ┌────────────┼────────────┐
              ▼            ▼            ▼
        RGAT-ABSA   四元组抽取    EASE (高效)
              │            │            │
              └────────────┼────────────┘
                           ▼
                  ABSA Survey (2023)
                           │
              ┌────────────┼────────────┐
              ▼            ▼            ▼
         ChatGPT-ABSA   SemEval 2022   MABSA (多模态扩展)
                           │
         ┌─────────────────┼─────────────────┐
         ▼                 ▼                 ▼
    融合方法          自监督预训练       域自适应/联邦
    (主线)           (减少标注)         (泛化+隐私)
         │                 │                 │
    逐层 → 多粒度 → 效率  掩码 → 对比 → 知识  经典 → VAE → 联邦
```

---

## 论文索引

详见 [[Categories/Multimodal-Sentiment]]
