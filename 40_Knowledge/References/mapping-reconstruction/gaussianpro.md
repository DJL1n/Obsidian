---
title: "GaussianPro: 3D Gaussian Splatting with Progressive Propagation"
tags:
  - paper
  - 3dgs
  - gaussian-splatting
  - multi-view-stereo
  - densification
---

# GaussianPro: 3D Gaussian Splatting with Progressive Propagation

## 基本信息
- 作者: Kai Cheng, Xiaoxiao Long, Kaizhi Yang, Yao Yao, Wei Yin, Yuexin Ma, Wenping Wang, Xuejin Chen
- 年份: 2024
- 来源: arXiv preprint
- arXiv: 2402.14650
- 链接: https://arxiv.org/abs/2402.14650
- 项目页: https://kcheng1021.github.io/gaussianpro.github.io

## 核心贡献

GaussianPro 解决了 3D Gaussian Splatting 的核心瓶颈之一：**SfM 初始化不足导致无纹理区域重建质量差**。传统 3DGS 严重依赖 SfM 产生的稀疏点云作为初始化，当场景存在大面积无纹理表面时，SfM 无法在这些区域生成足够的特征点，导致 3DGS 在这些区域优化困难、渲染质量低下。GaussianPro 从经典多视图立体视觉（MVS）中汲取灵感，提出 **progressive propagation** 策略，利用已有的重建几何先验和 patch matching 技术来引导 3D Gaussian 的 densification，而不是依赖简单的 split 和 clone 操作。

论文的关键创新在于：用 MVS 的几何传播思路替代 3DGS 的启发式 densification，让新 Gaussian 的位置和方向由多视图几何约束直接确定，而不是通过梯度优化盲目搜索。

## 方法概述

### Progressive Propagation Strategy
GaussianPro 的核心是 progressive propagation 机制。与 3DGS 原始的 split/clone 策略不同（基于 gradient 阈值来分裂或克隆 Gaussian），GaussianPro 借鉴 MVS 的思想：从已有的可靠几何点出发，通过多视图一致性约束，逐步向未覆盖区域"传播"新的 Gaussian。

具体流程：
1. **种子选择**：从 SfM 产生的稀疏点或已收敛的 Gaussian 作为初始种子
2. **Patch Matching**：对种子点周围的区域，在多视图中进行 patch matching，利用局部外观相似性和几何一致性来判断对应点
3. **几何验证**：通过 triangulation 和 reprojection error 验证新点的几何合理性
4. **渐进扩展**：将验证通过的点作为新的种子，继续向相邻区域传播，形成 wavefront 式的扩展

这种策略确保新生成的 Gaussian 具有良好的几何一致性，位置和方向由多视图观测共同约束，而非单靠梯度下降。

### 与 3DGS 原始 Densification 的对比
- 3DGS 原始方法：基于 view-space positional gradient 的阈值来做 split 和 clone，容易在无纹理区域产生虚假 Gaussian（因为没有梯度信号）
- GaussianPro：基于 MVS patch matching 的几何约束来 densify，即使无纹理区域也能通过多视图一致性找到合理的几何结构

### 参数优化
新传播的 Gaussian 继承了几何先验的初始参数（位置、方向、scale），后续仍然参与梯度优化，但初始值的质量远高于随机 split，收敛更快、更稳定。

## 实验结果

论文在大规模和中小规模场景上都进行了验证：

- **Waymo 数据集**（大规模自动驾驶场景）：相比原始 3DGS，PSNR 提升 1.15dB，显著改善了无纹理道路、建筑物平面的重建质量
- **Blender/ Tanks & Temples**（中小规模场景）：同样展现出优于 3DGS 的渲染质量，特别是在低纹理区域
- 消融实验证明 progressive propagation 的每个组件（patch matching、几何验证、渐进扩展）都对最终结果有正向贡献

## 优缺点分析

**优点：**
- 从根本上改进了 Gaussian 的初始化质量，不依赖无意义的 gradient threshold
- MVS 先验让新 Gaussian 具有几何合理性，收敛速度快
- 对无纹理区域的改善是实质性的（Waymo +1.15dB 是显著的）
- 渐进式传播可以控制 densification 的节奏，避免一次性生成过多冗余 Gaussian

**缺点：**
- Patch matching 的计算成本高于 split/clone，densification 阶段更慢
- 多视图一致性要求至少 2-3 个视角覆盖同一区域，极端稀疏视角下效果受限
- 对实时 SLAM 应用的适用性有待验证——propagation 是 offline 性质的
- 没有解决 Gaussian 过拟合的问题（与 AbsGS/ResGS 的方向互补）

## 方法继承

- [[40_Knowledge/References/mapping-reconstruction/resgs]] — ResGS 的 residual densification 思路与 GaussianPro 的渐进传播有异曲同工之处
- [[40_Knowledge/References/mapping-reconstruction/absgs]] — AbsGS 解决 gradient collision，GaussianPro 从源头改善了初始化的质量
- [[40_Knowledge/References/mapping-reconstruction/GSFusion]] — GSFusion 的 3DGS fusion 也借鉴了 MVS 思路
- [[40_Knowledge/References/geometry-model/MASt3R]] — MASt3R 提供 3D geometry prior 的思路与 GaussianPro 的 propagation 先验相通
- [[40_Knowledge/References/3dgs-slam/AnchorSplat]] — AnchorSplat 的 feed-forward 3D prior

## 相关笔记

- [[40_Knowledge/References/3dgs-slam/GS-SLAM]] — GS-SLAM 的 RGBD 场景下 depth 代替 SfM 提供几何
- [[40_Knowledge/References/3dgs-slam/Splat-SLAM]] — Splat-SLAM 的 densification 策略
- [[40_Knowledge/References/mapping-reconstruction/fastgs]] — FastGS 的加速策略
- [[40_Knowledge/References/geometry-model/DUSt3R]] — DUSt3R 的 multi-view geometry estimation

## 所属分类

3D Gaussian Splatting / Multi-view Stereo / Scene Reconstruction / Densification

---

## 与 SkelGS-SLAM 的关联

GaussianPro 的 progressive propagation 对 SkelGS-SLAM 的 anchor-to-Gaussian 映射有参考意义：
- SkelGS 的 anchor 可以看作 GaussianPro 中"种子点"的升级版——anchor 是更高层级的几何结构
- GaussianPro 的 wavefront 式扩展可以用于 SkelGS 中从 anchor 向周围区域的 Gaussian 传播策略
- 两者都强调用几何先验指导 Gaussian 的放置，而不是依赖 gradient-based 的盲目优化
