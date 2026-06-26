# Asynchronous Methods for Deep Reinforcement Learning (A3C)

> arXiv:1602.01783 | 2016

## 核心贡献

提出异步深度强化学习方法 A3C（Asynchronous Advantage Actor-Critic），通过异步梯度更新实现了高效、可扩展的并行 RL 训练。A3C 的关键洞察是：多个异步并行的 actor-learner 无需显式通信即可实现隐式的探索多样化，因为每个 worker 在环境中的不同位置独立探索。A3C 在 Atari 游戏、MuJoCo 连续控制和 3D 第一人称导航（3D Maze）等任务上均达到 SOTA 性能，且计算开销远低于 DQN 的多步训练。

## 方法

A3C 的核心设计：
1. **Actor-Critic 架构**：每个 worker 同时维护策略（actor）和价值（critic）网络，使用 advantage 函数 A(s,a) = r + γV(s') - V(s) 降低策略梯度的方差
2. **异步更新**：K 个 worker 各自在独立的环境中前推若干步（K=5），收集轨迹后计算梯度并异步更新全局网络。没有经验回放（experience replay），每个 worker 使用自己的轨迹进行更新
3. **多种变体**：论文提出四种异步算法——A3C（基于 advantage 的 actor-critic）、A2C（基于 TD-error 的 actor-critic）、AC（纯策略梯度）和异步 N-step DQN。A3C 是其中表现最好的

每个 worker 拥有全局网络的私有副本，局部更新后直接推送全局参数，多个 worker 的并行更新提供了类似噪声注入的探索效果。

## 影响

A3C 是第一个将并行化与 actor-critic 架构结合的 RL 算法，其设计简洁且实用。异步更新的思想深刻影响了后续工作：A2C（同步版）成为多环境 RL 的标准，A3C 的并行探索机制启发了 IMPALA、R2D2 等算法。A3C 也是 PPO 的前身——PPO 在 A3C 的 actor-critic 基础上引入了 clip 机制和优势估计的 GAE（Generalized Advantage Estimation）。A3C 在 3D Maze 上的零样本泛化能力首次展示了 RL 在复杂导航任务中的潜力，为后来的 AI 导航研究开辟了方向。

## 所属分类

[[Categories/RL-Foundations]]
