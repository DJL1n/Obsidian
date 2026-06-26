# Soft Actor-Critic: Off-Policy Maximum Entropy Deep Reinforcement Learning with a Stochastic Actor

> arXiv:1801.01290 | 2018

## 核心贡献

提出 Soft Actor-Critic（SAC），一种离策略（off-policy）的最大熵深度强化学习算法。SAC 将最大熵 RL 框架与 actor-critic 架构结合，在优化期望累积奖励的同时最大化策略的熵（随机性），从而实现探索与利用的平衡。SAC 在连续控制任务上表现优异，相比 DDPG 等经典算法具有更高的样本效率和更稳定的收敛性，同时支持使用离线数据经验回放进行训练。

## 方法

SAC 的核心设计包括三个关键组件：
1. **最大熵目标**：优化目标为 J(π) = E[∑(r(s,a) + αH(π(·|s)))], 其中 α 是温度参数控制熵的权重，H 是策略熵。温度参数 α 可以被自动调节，通过约束熵目标为常数
2. **软 Q 函数**：与传统 Q 学习不同，软 Q 函数定义为 Q(s,a) = r(s,a) + V(s)，其中 V(s) = E_a∼π[Q(s,a)] - α log π(a|s)，即价值函数包含策略的负对数概率
3. **双 Q 网络**：使用两个独立的 Q 网络并取最小值（twin Q），防止 Q 值过度高估

策略通过 reparameterized trick 进行梯度估计，支持 Gaussian 和 Squashed Gaussian 两种策略分布。

## 影响

SAC 已成为连续控制任务的事实标准算法之一，其最大熵框架影响深远。SAC 的改进版本 SAC-ADAPT、TSAC 等进一步优化了温度参数自适应和训练效率。SAC 与 PPO 形成互补——SAC 更适合连续控制、离线 RL 场景，PPO 更适合离散策略和在线训练。SAC 的最大熵思想还被应用到模仿学习、多任务学习以及 RLHF（如 DPO）中。SAC 在 MuJoCo 基准上达到 SOTA 级别性能，且代码实现简洁，被广泛集成到 rllib、stable-baselines3 等 RL 框架中。


## 相关笔记

- [[DQN]]

## 所属分类

[[Categories/RL-Foundations]]
