---
title: For SALE: State-Action Representation Learning for Deep Reinforcement Learning
tags:
  - paper
  - reinforcement-learning
  - representation-learning
  - continuous-control
  - offline-RL
---

# For SALE: State-Action Representation Learning for Deep Reinforcement Learning

## 基本信息
- 作者: Scott Fujimoto, Wei-Di Chang, Edward J. Smith, Shixiang Shane Gu, Doina Precup, David Meger
- 年份: 2023
- 来源: arXiv (TMLR)
- 链接: https://arxiv.org/abs/2306.02451

## 核心贡献

本文的核心贡献在于将表征学习（Representation Learning）系统地引入到低维状态空间的深度强化学习中，填补了该领域长期被忽视的一个空白。长期以来，RL社区中的表征学习研究几乎全部集中在图像/视觉输入（如DQN中的CNN特征提取、Pixel-level representation learning等），而对于MuJoCo等物理控制任务中已经提供的低维连续状态向量，研究者普遍认为不需要额外的表征学习。SALE挑战了这一假设，提出即使输入是低维状态，学习一个精心设计的state-action联合嵌入表示也能显著提升RL算法的性能。论文进一步将这些嵌入与"checkpoint"机制结合到TD3中，形成了TD7算法——这一命名暗示了从TD3到TD7的迭代进化路线。SALE的设计哲学是将"状态-动作"的交互信息编码到潜在空间中，而非仅分别编码状态和动作，从而让策略和Q网络能够利用更丰富的联合语义信息做出决策。

## 方法概述

SALE的核心是设计一个状态-动作联合嵌入模块（State-Action Embedding），该模块接收原始状态s和动作a，输出一个低维的联合潜在表示z=f_θ(s,a)。论文系统地探索了该嵌入模块的设计空间，包括：网络结构（MLP层数、宽度、激活函数）、潜在空间维度、归一化策略（LayerNorm vs BatchNorm）、以及嵌入在RL pipeline中的注入位置。关键设计发现包括：(1) 联合嵌入(s,a)比分别嵌入(s)和(a)效果更好，因为状态和动作之间的交互是决策的核心；(2) 适度的潜在维度（如32-64维）在性能和效率间取得最佳平衡；(3) LayerNorm在离线RL场景下优于BatchNorm，避免了batch分布漂移问题。SALE被集成到TD3算法中形成TD7：TD7在原有的actor-critic架构基础上，在critic网络的输入端加入SALE嵌入模块，同时在训练过程中使用checkpoint机制保存和恢复优化状态。TD7支持在线和离线两种训练模式，在离线设置下利用静态数据集进行训练，在线设置下通过环境交互持续更新。

## 实验结果

SALE/TD7在OpenAI Gym标准连续控制基准上展示了显著的性能提升。与基线TD3相比，TD7在300k时间步时的平均性能提升高达276.7%，在5M时间步时仍有50.7%的提升。这一差距在早期训练阶段尤为显著，说明SALE嵌入有效加速了学习过程。论文进行了全面的消融实验，验证了各个设计选择的贡献：联合嵌入vs分离嵌入、不同潜在维度、归一化策略等。在离线RL设置下，TD7同样表现出色，证明了SALE嵌入在数据效率方面的优势——模型能够从有限的离线数据中提取更多有用的表征信息。论文还与SAC、DDPG、PPO等其他主流算法进行了对比，结果显示TD7在多个任务上超越或至少匹敌这些SOTA方法。实验涵盖了Hopper、Walker、HalfCheetah、Ant等经典MuJoCo任务，以及Humanoid等高维控制任务，验证了方法的广泛适用性。

## 优缺点分析

优点方面，SALE最大的优势是其简单性和即插即用特性——只需在现有RL算法中添加一个轻量级嵌入模块即可获得显著性能提升，这大大降低了应用门槛。第二个优势是泛化能力，SALE/TD7在在线和离线两种模式下均有效，且适用于从低维到高维的各种控制任务。从理论角度看，SALE将"state-action联合表征"的概念正式化，为后续RL表征学习研究提供了清晰的分析框架。然而，缺点同样值得注意：首先，SALE本质上仍是一个MLP嵌入，其创新性更多在于"发现低维状态也需要表征学习"这一洞见，而非提出全新的表征学习方法；其次，论文虽然系统探索了设计空间，但对为什么joint embedding优于separate embedding缺乏更深层的理论解释（如信息论视角下的互最大化分析）；最后，SALE在离散动作空间和视觉输入场景下的泛化性未在论文中验证。从研究角度看，SALE为RL表征学习打开了一个新方向，但后续工作需要更深入的理论分析和更广泛的实验验证。

## 方法继承

- [[SAC]] — SALE与SAC同样关注连续控制任务，但SAC使用最大熵框架，SALE使用联合表征学习；两者可结合使用
- [[DQN]] — DQN开创了视觉输入的RL表征学习，SALE将这一思想扩展到低维状态空间
- [[VAE]] — VAE的潜在空间学习方法论与SALE的表征设计思路在概念上相通，都关注信息压缩与重建
- [[beta-VAE]] — β-VAE的解耦表征思想可借鉴到SALE的状态-动作联合嵌入设计中

## 相关笔记

- [[Offline-RL]] — TD7的离线RL模式与离线强化学习的研究直接相关
- [[RL-Foundations]] — 作为RL基础研究的一部分，SALE对RL表征学习的系统性探索属于RL基础范畴
- [[Dueling-DQN]] — Dueling DQN的state-value与advantage分解与SALE的state-action联合嵌入在表征设计哲学上有相似之处

## 所属分类

[[Categories/RL-Foundations]]
