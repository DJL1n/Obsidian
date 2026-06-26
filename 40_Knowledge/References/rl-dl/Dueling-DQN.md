# Dueling Network Architectures for Deep Reinforcement Learning

> arXiv:1511.06581 | 2015

## 核心贡献

提出 Dueling DQN 架构，将深度强化学习中 Q 网络的价值函数分解为状态价值函数 V(s) 和优势函数 A(s,a) 两条独立路径（stream），使智能体在不需要显式策略的情况下学习到状态的价值估计。该设计让网络在"哪些状态是好的"和"哪些动作在该状态下更好"之间进行结构化的解耦。实验证明，在 Atari 等多个环境中，Dueling DQN 比标准 DQN 获得更高的最终回报，且训练更稳定。

## 方法

在标准 DQN 的 CNN 特征提取器之后，网络分裂为两条独立的全连接层：一条估计状态价值 V(s)，另一条估计优势函数 A(s,a)。两条路径的输出通过特定的聚合方式重新组合成 Q(s,a)。论文提出了三种聚合方式：
- **Dueling-Net Sum**: Q(s,a) = V(s) + A(s,a)，其中 A 被减去均值以保证分解唯一性
- **Dueling-Net Min**: Q(s,a) = V(s) + min(A(s,a))，利用 min 的次可加性
- **Dueling-Net Max**: Q(s,a) = V(s) + max(A(s,a))，利用 max 的次可加性

实验中最常用的是 Sum 方式。网络结构在大部分 Atari 游戏中使用与 DQN 相同的 CNN 前端，仅在后端增加双路结构。

## 影响

Dueling DQN 是 DQN 系列的重要改进之一，与 DDPG、PPO 等算法形成对比——它展示了通过**网络结构设计**而非改变优化目标或更新机制来提升强化学习性能的思路。其价值-优势分解的思想后来被广泛借鉴到策略梯度方法（如 A3C、PPO 中的 advantage estimation）以及各种多智能体 RL 架构中。Dueling 结构也是后来 DeepMind 的 Rainbow DQN 算法的五个核心改进之一，成为现代 DQN 变体的标准组件。
