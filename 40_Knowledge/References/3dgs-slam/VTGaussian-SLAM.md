---
title: "VTGaussian-SLAM: RGBD SLAM for Large Scale Scenes with Splatting View-Tied 3D Gaussians"
tags:
  - paper
  - gs-slam
  - rgb-d
  - 3dgs
  - icml-2025
  - large-scale
---

# VTGaussian-SLAM: RGBD SLAM for Large Scale Scenes with Splatting View-Tied 3D Gaussians

## 基本信息
- 作者: Pengchong Hu, Zhizhong Han
- 年份: 2025
- 来源: ICML 2025
- arXiv: 2506.02741
- 链接: https://arxiv.org/abs/2506.02741

## 核心贡献

VTGaussian-SLAM 针对 RGBD SLAM 中大尺度场景的扩展性问题，提出了 **view-tied 3D Gaussians** 这一简化的 Gaussian 表示方式。传统 3D Gaussian Splatting 需要为每个 point 学习位置、旋转和多维度协方差参数，导致 GPU 内存随场景规模线性增长，无法处理大型场景。VTGaussian 将 Gaussian 直接绑定到 depth pixel 上，不再需要学习位置、旋转和协方差参数，从而大幅减少存储需求和优化参数。这种方法让系统能够在有限的 GPU 内存中使用数量多得多的 Gaussian 来表示局部细节，实现了可扩展的大场景 SLAM。

论文的核心洞察是：对于 RGBD 数据，depth 已经提供了像素级的几何信息，Gaussian 的位置和协方差可以从 view 和 depth 直接推导，不需要作为可学习参数。这个简化不仅减少了优化空间，还消除了大量梯度计算和内存占用。

## 方法概述

### View-Tied 3D Gaussians
将 3D Gaussian 与 RGBD 图像的深度像素绑定。每个 Gaussian 的位置由相机位姿和深度值反投影确定，协方差由深度像素的投影几何决定，不需要独立学习。这种设计使得 Gaussian 的数量可以远超传统方法，因为每个 Gaussian 只存储颜色和可渲染性参数，而不是完整的 6 个位置+旋转+协方差自由度。

### 跟踪策略
提出新的 tracking 策略，不需要在每次优化时维护所有 Gaussian 的可学习状态。通过选择性的 Gaussian 激活和冻结机制，只优化当前帧相关的局部 Gaussian 子集来估计相机位姿。这避免了全局优化时的内存爆炸问题。

### 建图策略
新的 mapping 策略同样采用局部激活的方式，新增的 keyframe 只引入其对应的 view-tied Gaussian，不需要重新优化历史帧的 Gaussian。由于 Gaussian 与 view 绑定，新视角自然带来新的 Gaussian 集合，历史 Gaussian 作为参考保持冻结状态。

### 大规模场景扩展
通过将场景分割为 view-tied 的局部 patch，系统可以在有限内存中处理远超之前方法的场景规模。渲染时只加载当前相机视野相关的 Gaussian 子集，实现了类似 foveated rendering 的效果。

## 实验结果

论文在标准 RGBD SLAM 基准上验证了方法的有效性，包括 TUM RGBD、IROS/ICML 相关数据集。实验表明 VTGaussian-SLAM 在跟踪精度（ATE）、渲染质量（PSNR/SSIM/LPIPS）和可扩展性三个方面都优于现有方法。特别是在大尺度场景中，传统 GS-SLAM 方法因为内存限制无法完整处理，而 VTGaussian-SLAM 能够稳定运行。

## 优缺点分析

**优点：**
- View-tied 设计极大简化了 Gaussian 表示，每个 point 只需要少量参数
- 天然适合 RGBD 输入，depth 提供了精确的几何先验
- 可扩展到大场景，内存占用可控
- 跟踪和建图采用局部策略，避免全局优化的复杂度

**缺点：**
- 依赖 RGBD 输入，无法直接迁移到纯 monocular 场景
- View-tied 限制了 Gaussian 的灵活性——对于需要 sub-pixel 精度的场景可能不足
- 当 depth 噪声较大时，反投影的位置误差会直接影响 Gaussian 质量
- 局部优化策略可能导致全局一致性漂移，需要 loop closure 机制

## 方法继承

- [[40_Knowledge/References/3dgs-slam/GS-SLAM]] — GS-SLAM 的 Gaussian mapping 框架
- [[40_Knowledge/References/3dgs-slam/Splat-SLAM]] — Splat-SLAM 的 densification 策略
- [[40_Knowledge/References/3dgs-slam/GigaSLAM]] — GigaSLAM 的大场景分层方法，VTGaussian 的 submap 思路类似
- [[40_Knowledge/References/3dgs-slam/SING3R-SLAM]] — SING3R 的 submap-based design 与 view-tied 的局部策略思想相通
- [[40_Knowledge/References/mapping-reconstruction/fastgs]] — FastGS 的加速思路
- [[40_Knowledge/References/mapping-reconstruction/resgs]] — ResGS 的 densification 策略

## 相关笔记

- [[40_Knowledge/References/3dgs-slam/VarSplat]] — VarSplat 的 uncertainty-aware design
- [[40_Knowledge/References/3dgs-slam/AnchorSplat]] — AnchorSplat 的 3D geometric prior
- [[40_Knowledge/References/Categories/RGBD-GS-SLAM]] — RGBD GS-SLAM 分类索引
- [[40_Knowledge/References/survey/CoGS-SLAM-Survey]] — GS-SLAM 综述

## 所属分类

GS-SLAM / RGBD SLAM / 3D Gaussian Splatting / 大尺度场景重建

---

## 与 SkelGS-SLAM 的关联

VTGaussian 的 view-tied design 与 SkelGS-SLAM 的 anchor-based approach 有异曲同工之处：两者都试图减少 Gaussian 的自由度，通过几何先验来约束优化空间。区别在于：
- VTGaussian 将 Gaussian 绑到 depth pixel（数据驱动）
- SkelGS-SLAM 将 Gaussian 绑到 anchor point（结构驱动）

SkelGS-SLAM 可以考虑借鉴 view-tied 的参数简化思路，在 anchor 框架内实现更高效的表示。同时 VTGaussian 的局部跟踪策略对 SkelGS 的大场景扩展有参考价值。
