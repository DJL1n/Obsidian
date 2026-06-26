# Training language models to follow instructions with human feedback (InstructGPT / RLHF)

> arXiv:2203.02155 | 2022

## 核心贡献

由 OpenAI 提出的 RLHF（Reinforcement Learning from Human Feedback）方法，首次系统性地展示了如何通过人类偏好数据对大语言模型进行对齐训练，使其更好地遵循用户指令。该方法产生了 InstructGPT，是 GPT-3 的指令微调版本，后来启发了 ChatGPT、Claude 等对话式 AI 系统的训练范式。论文证明，仅靠模型规模扩大不足以让语言模型学会跟随指令，需要通过人类反馈来引导模型行为。

## 方法

RLHF 训练流程分为三个步骤：
1. **Supervised Fine-tuning（SFT）**：使用人工标注的指令-回复对对基座语言模型进行微调，让模型初步学会遵循指令的格式和内容
2. **Reward Model 训练**：人类标注者对同一指令的多个模型回复进行排序，收集偏好数据。训练一个奖励模型（reward model）来预测人类偏好，即给定指令和回复，输出一个标量奖励值
3. **RL 优化（PPO）**：使用近端策略优化（PPO）算法，以 reward model 的输出作为奖励信号，优化语言模型生成回复的策略。PPO 的损失函数中包含 KL 散度惩罚项，防止模型偏离预训练分布太远

实验在 OpenAI WebSummarize 和 RealToxicityPrompts 数据集上验证，InstructGPT 在总结质量和毒性降低方面均优于直接微调。

## 影响

RLHF 是对齐领域的开创性工作，确立了"人类反馈 → 奖励模型 → RL 优化"的标准范式，被几乎所有后续 LLM 对齐方法（如 PPO、DPO、ORPO、KTO）所借鉴和进化。RLHF 直接推动了 ChatGPT 的诞生，开启了对话式 AI 时代。其局限性（昂贵的标注成本、reward hacking、单轮 vs 多轮偏好等）也催生了 DPO（Direct Preference Optimization）等更高效的替代方法。RLHF 是 AI 对齐领域最重要的工作之一。

## 所属分类

[[Categories/RL-Alignment]]
