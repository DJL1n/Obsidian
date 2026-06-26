# Playing Atari with Deep Reinforcement Learning (DQN)

> arXiv:1312.5602 | 2013

## 核心贡献

提出 Deep Q-Network（DQN），首次将深度神经网络与 Q-learning 结合，在无需任何先验知识的情况下，仅凭原始像素输入就能以人类水平掌握 Atari 2600 游戏的策略。DQN 在多个 Atari 游戏上达到了与人类玩家相当甚至超越的性能，标志着深度强化学习（DRL）的诞生。论文提出了两个关键技术——经验回放（experience replay）和目标网络（fixed Q-targets）——来解决传统 RL 在深度学习中遇到的训练不稳定问题。

## 方法

DQN 的核心设计：
1. **端到端 Q 网络**：使用卷积神经网络直接从 Atari 游戏的帧（4 个连续帧拼接）预测每个动作的 Q 值，输出层为 Atari 2600 的 18 个可能动作
2. **经验回放（Experience Replay）**：将智能体与环境交互的转移样本 (s, a, r, s') 存储到回放缓冲区中，训练时随机采样 mini-batch 打破数据之间的时间相关性，降低梯度方差
3. **固定目标（Fixed Q-targets）**：每 C 步（C=10000）冻结目标 Q 网络的权重，消除目标值随策略变化而移动的问题
4. **奖励裁剪**：将所有奖励裁剪到 [-1, 0, +1]，防止奖励尺度过大导致训练不稳定

网络结构为三层 CNN，使用 ReLU 激活函数，输出层为线性激活。

## 影响

DQN 是深度强化学习的开山之作，开创了"端到端感知 → 决策"的范式。经验回放和目标网络成为后续所有 DRL 算法的标准组件。DQN 的成功激发了大量后续工作：Double DQN（解决过估计问题）、Dueling DQN（价值-优势分解）、Prioritized Experience Replay（重要采样）、Rainbow DQN（集成所有改进）。DQN 的影响远超 RL 领域，它证明了深度学习和强化学习可以结合并在复杂任务上取得突破性进展。


## 相关笔记

- [[SAC]]

## 所属分类

[[Categories/RL-Foundations]]
