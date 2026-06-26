# Agent Lighting: Illuminating Agents in the Dark

> arXiv:2503.19761 | 2025

## 核心贡献

Agent Lighting 提出了基于强化学习的 LLM 智能体训练框架，旨在提升多步推理和工具使用场景下智能体的规划能力和鲁棒性。该方法将智能体训练视为"在黑暗中照亮路径"的过程，通过稀疏奖励信号引导 LLM 智能体学习有效的环境交互策略，在需要多步推理和外部工具调用的复杂任务上显著超越标准 prompt-based 方法。

## 方法

核心框架包括：
1. **ReAct 格式的策略表示**：智能体输出"Thought-Action-Observation"序列，将思维链和工具调用联合建模
2. **基于规则的奖励设计**：为不同任务类型（数学推理、代码执行、网页浏览等）设计细粒度的奖励函数，包括步骤奖励、格式奖励和结果奖励
3. **GRPO 优化**：使用 Group Relative Policy Optimization 在无需 critic 网络的情况下直接优化智能体策略，通过组内相对优势估计进行策略更新
4. **课程学习（Curriculum Learning）**：从简单任务逐步过渡到复杂任务，帮助智能体建立基础的推理和工具使用能力后再进行复杂任务训练

## 影响

Agent Lighting 展示了 RL 在 LLM 智能体训练中的有效性，是 R1 之后又一个重要的 RL-for-agents 工作。其设计特别针对工具使用和规划任务，与 DeepMind 的 "Reinforcement Learning for Foundational Models" 和 Anthropic 的 "Process Reward Models" 方向相呼应。该工作推动了对智能体"思维-行动"联合优化方法的关注，为自主智能体的可靠部署提供了新的技术路径。

## 所属分类

[[Categories/RL-Alignment]]
