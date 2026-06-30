---
title: "Generative 3D Gaussians with Learned Density Control (DeG)"
tags:
  - paper
  - 3dgs
  - generative
  - diffusion
  - density-control
  - siggraph-2026
---

# Generative 3D Gaussians with Learned Density Control (DeG)

## 基本信息
- 作者: Runjie Yan, Yan-Pei Cao, Peng Wang, Ding Liang, Yuan-Chen Guo
- 年份: 2026
- 来源: SIGGRAPH Conference Papers 2026
- arXiv: 2605.16355
- 链接: https://arxiv.org/abs/2605.16355
- 类别: cs.GR, cs.CV

## 核心贡献

论文提出了 **Density-Sampled Gaussians (DeG)**，一种将 3D Gaussian 的自适应密度控制与可学习的生成模型统一的表示方法。核心创新有两层：

1. **DeG 表示**：将 Gaussian center 建模为 octree 上可学习的概率密度函数的样本，通过联合优化空间密度和 Gaussian 属性，让 primitive 自动在高几何复杂度区域集中。用可微的 render loss gradient 替代了 3DGS 中离散的 densification/pruning 启发式操作。

2. **VecSeq 重索引**：将无序的 Gaussian latent 集合映射到确定性 3D Sobol 序列上，把 set-generation 问题转化为 sequence modeling，让 diffusion 模型能在无序结构化数据上稳定收敛。

最终训练 DeG 的 latent diffusion model，实现 single-image-to-3D 生成，达到 SOTA 质量。

## 方法概述

### DeG: Density-Sampled Gaussians
不同于把 Gaussian 约束在固定 voxel grid 或 array 的方法，DeG 将 Gaussian center 定义为从 octree 上可学习的概率密度函数中采样的点。这意味着：

- **自适应密度**：高密度区域自动分配更多 Gaussian，低密度区域稀疏
- **可微密度控制**：通过 render loss 的梯度来调整概率密度分布，替代 split/prune 的离散操作
- **变量分辨率**：同一个 latent code 可以通过调整采样 budget 输出不同分辨率的 3D 表示

### Learnable PDF over Octree
Octree 提供层次化的空间结构，每个节点的概率可以学习。采样时从根节点递归向下，根据概率分布决定在哪采样 Gaussian center。这种设计让密度控制有了严格的数学框架，而不是启发式的 gradient threshold。

### VecSeq: Canonical Re-indexing
Diffusion model 通常处理有序序列（如 token sequence, pixel grid）。但 DeG 的 latent 是无序的 Gaussian set，直接喂给 diffusion 会面临两个问题：
1. 训练不稳定——相同的 set 不同排列产生不同输出
2. 收敛极慢——模型需要学习排列不变性

VecSeq 把每个 Gaussian token 锚定到一个确定性的 3D Sobol 序列位置上，实现 canonical 的 ordering。这样 set generation 变成了 sequence modeling，diffusion 可以稳定训练。

### Latent Diffusion for 3D Generation
在 DeG latent space 上训练 diffusion model，输入单图特征，输出完整的 3D Gaussian 场景。整个 pipeline：
1. 编码器：单图 → DeG latent
2. Diffusion：latent 去噪
3. 解码器：DeG latent → 3D Gaussian 场景 → 渲染

## 实验结果

论文在 single-image-to-3D 生成任务上展示了 SOTA 质量：
- 结合了 unstructured primitives 的结构适应性和 grid-based methods 的训练稳定性
- 变量分辨率解码支持不同质量/速度的 trade-off
- 在标准基准上超越现有方法

## 优缺点分析

**优点：**
- DeG 的密度控制有严格数学框架，比启发式方法更优雅
- VecSeq 解决了无序 set 的 diffusion 训练问题，通用性强
- 变量分辨率是一个实用特性，可以根据资源动态调整输出质量
- 统一了 adaptive rendering 和 generative modeling 两个方向

**缺点：**
- Octree 采样有计算开销，训练比固定 grid 方法慢
- DeG 的 density 函数本身需要学习，增加了优化难度
- VecSeq 的 Sobol 序列锚定可能引入伪影（如果 latent 的真实分布与 Sobol 序列不匹配）
- 目前只在生成任务上验证，在 SLAM/mapping 场景的适用性未知

## 方法继承

- [[40_Knowledge/References/mapping-reconstruction/resgs]] — ResGS 的 residual densification 与 DeG 的自适应密度控制思路相通
- [[40_Knowledge/References/mapping-reconstruction/absgs]] — AbsGS 解决 gradient collision，DeG 从密度层面避免了盲目 densification
- [[40_Knowledge/References/mapping-reconstruction/gaussianpro]] — GaussianPro 的 progressive propagation 也是一种几何引导的 densification
- [[40_Knowledge/References/foundation-models/Stable-Diffusion]] — DeG 的 latent diffusion 架构建立在 Stable Diffusion 范式上
- [[40_Knowledge/References/geometry-model/MASt3R]] — MASt3R 的 feed-forward 3D 估计与 DeG 的单图到 3D 生成有交叉

## 相关笔记

- [[40_Knowledge/References/3dgs-slam/AnchorSplat]] — AnchorSplat 的 3D prior 引导思路
- [[40_Knowledge/References/3dgs-slam/SplaTAM]] — SplaTAM 的 Gaussian mapping
- [[40_Knowledge/References/matching-representation/Scaffold-GS]] — Scaffold-GS 的 scaffold-based 组织与 DeG 的 octree 结构类似
- [[40_Knowledge/References/geometry-model/VGGT]] — VGGT 的 feed-forward 几何估计

## 所属分类

3D Gaussian Splatting / Generative 3D / Diffusion Model / Adaptive Density Control

---

## 与 SkelGS-SLAM 的关联

DeG 的核心思路——用可学习密度函数替代启发式 densification——对 SkelGS 有直接参考意义：
- SkelGS 的 anchor 可以看作是 DeG octree density 的离散化版本
- DeG 的变量分辨率采样暗示 SkelGS 可以在不同区域用不同密度的 anchor 来控制复杂度
- VecSeq 的 canonical ordering 对 SkelGS 中如何给 anchor 编号、做 consistent indexing 有启发
