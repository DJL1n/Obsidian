# Offline Reinforcement Learning: Tutorial and Review

> arXiv:2311.16432 | 2023

## 核心贡献

提供离线强化学习（Offline RL / Batch RL）的全面综述和教程。离线 RL 的核心挑战是在仅使用固定数据集（不与环境交互）的情况下学习最优策略，避免分布外（out-of-distribution, OOD）动作的过估计问题。论文系统梳理了离线 RL 的理论基础、算法分类、评估协议和开放问题，为该领域的研究者和工程师提供了完整的参考指南。

## 方法

论文将离线 RL 算法分为三大类：
1. **约束策略（Policy Constraint）**：限制策略在数据覆盖范围内，如 CQI（Constrained Q-Iteration）、CQL（Conservative Q-Learning）通过惩罚 OOD 动作的 Q 值
2. **约束值函数（Value Constraint）**：在值函数估计中引入正则化，如 Bellman 一致性惩罚（IQL）、不确定性加权
3. **行为约束（Behavior Constraint）**：通过重要性采样或密度比估计限制策略分布，如 BCQ（Batch-Constrained deep Q-learning）、AWAC（Advantage-Weighted Actor-Critic）

教程部分详细解释了离线 RL 的评估协议（如 D4RL 基准）、数据收集策略以及离线到在线的部署问题。

## 影响

该综述是离线 RL 领域最全面的参考资料之一，帮助研究者和工程师快速理解该领域的算法全貌。离线 RL 在机器人学习、医疗决策、推荐系统等无法进行在线探索的领域具有重要应用价值。论文中对 CQL、IQL、AWAC 等算法的比较分析为算法选择提供了实用指导，推动了离线 RL 从理论研究向实际应用的转化。

## 所属分类

[[Categories/RL-Foundations]]
