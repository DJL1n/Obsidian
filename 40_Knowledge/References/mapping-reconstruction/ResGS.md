---
title: "ResGS: Residual Densification of 3D Gaussian for Efficient Detail Recovery"
tags:
  - paper
  - 3DGS
  - densification
  - novel-view-synthesis
  - mapping-reconstruction
---

# ResGS: Residual Densification of 3D Gaussian for Efficient Detail Recovery

## 基本信息
- 作者: Yanzhe Lyu, Kai Cheng, Xin Kang, Xuejin Chen
- 年份: 2024
- 来源: arXiv (cs.CV)
- arXiv链接: https://arxiv.org/abs/2412.07494

## 核心贡献

ResGS 的核心贡献在于对 3D Gaussian Splatting（3D-GS）中**自适应密度控制（Adaptive Density Control）策略**的根本性反思与改进。原始 3D-GS 的 densification 操作通过梯度阈值判断是否克隆或分裂 Gaussian，但 ResGS 团队发现这种策略存在一个本质困境：在几何覆盖（geometry coverage）与细节恢复（detail recovery）之间存在不可调和的矛盾。具体来说，当场景中存在大量高频细节时，标准 split 操作会将一个大 Gaussian 等分为两个相同尺度的子 Gaussian，这导致原始 Gaussian 所覆盖的粗粒度几何区域可能出现空洞或模糊，因为 split 后的子 Gaussian 无法完全继承父 Gaussian 的空间覆盖范围。同时，对于已经被充分优化的区域，继续盲目 split 会造成冗余计算。

ResGS 提出了一种全新的**残差分裂（residual split）**操作：在进行 Gaussian 分裂时，不直接替换原始 Gaussian，而是保留一个缩小比例的父 Gaussian 作为"残差"（residual），同时添加一个子 Gaussian 用于捕获高频细节。这种"保留-添加"策略使得粗粒度几何覆盖得以维持，而新增的残差 Gaussian 专注于恢复局部细节，从根本上解耦了几何覆盖与细节恢复的矛盾。

除此之外，ResGS 引入了**高斯图像金字塔（Gaussian image pyramid）**作为渐进式监督信号，从低分辨率到高分辨率逐层优化 Gaussian 参数。金字塔的底层提供全局几何约束，避免局部优化陷入过拟合；高层提供细节约束，确保纹理和高频结构被正确重建。配合一种优先对粗粒度 Gaussian 进行 densification 的选择机制，ResGS 实现了训练过程中计算资源的高效分配。

该方法的另一个关键贡献在于其**通用性**——残差分裂可以被即插即用地应用到各种 3D-GS 变体中，在多个基准上均带来了稳定的性能提升，证明了其作为 3D-GS 基础设施改进的潜力。

## 方法概述

ResGS 的方法框架由三个核心组件构成：**残差分裂操作**、**高斯图像金字塔渐进监督**，以及**粗粒度优先的 densification 选择策略**。

**残差分裂（Residual Split）**：当某个 Gaussian 的梯度超过分裂阈值时，标准 3D-GS 会将其 clone 并沿最大方差方向 split 为两个等尺度子 Gaussian，然后衰减父 Gaussian 的不透明度直至删除。ResGS 的做法是：保留原始 Gaussian 但按比例缩小其尺度（作为残差），同时新增一个子 Gaussian 继承原始位置并在最大方差方向偏移。这样，父 Gaussian 继续提供该区域的粗粒度覆盖，子 Gaussian 则聚焦于捕获父 Gaussian 无法表达的局部高频变化。数学上，残差分裂可以看作是"parent → parent(rescaled) + child"的操作，而非"parent → child1 + child2"。这种设计确保了在任何时刻，场景的几何覆盖都不会因为 densification 而出现空洞。

**高斯图像金字塔（Gaussian Image Pyramid）**：受图像金字塔思想的启发，ResGS 在训练过程中构建了多分辨率的监督层级。在训练早期，使用低分辨率的渲染图像进行监督，此时 Gaussian 主要学习全局几何结构和粗略的颜色分布。随着训练进行，逐步提高监督分辨率，Gaussian 开始细化纹理和边缘。金字塔的构建方式是对输入图像进行下采样得到多个分辨率层级，每个层级对应不同的训练阶段。在低分辨率层级，由于图像像素较少，每个像素对应的 3D 区域较大，因此 Gaussian 不需要过度细化就能获得较低的渲染误差；在高分辨率层级，像素对应更小的 3D 区域，迫使 Gaussian 进行更精细的分裂和位置调整。这种渐进式策略避免了训练初期的过度 densification，同时也确保了训练后期的细节充分性。

**粗粒度优先选择策略（Coarse-first Selection）**：在每一轮 densification 中，ResGS 优先选择尺度较大（即更粗粒度）的 Gaussian 进行分裂，而非均匀地处理所有超过阈值的 Gaussian。其直觉是：粗粒度 Gaussian 对场景的整体几何覆盖影响更大，优先分裂它们可以在早期快速建立合理的场景结构，而细粒度 Gaussian 的细节恢复则留到训练后期。这种策略通过 Gaussian 尺度排序实现，确保训练过程中计算资源首先投入到对全局结构最有影响的操作上。

在实现层面，ResGS 对原始 3D-GS 的代码修改非常少，核心改动集中在 densification 逻辑中，将其从标准 split 替换为 residual split，并加入金字塔分辨率调度和粗粒度排序。这使得 ResGS 可以轻松集成到现有的 3D-GS pipeline 中。

## 实验结果

ResGS 在多个标准基准上进行了全面评估，主要数据集包括 Mip-NeRF 360、Tanks & Temples 和 Deep Blending。

**渲染质量**：ResGS 在 PSNR、SSIM 和 LPIPS 三个指标上均达到了 state-of-the-art 水平。在 Mip-NeRF 360 数据集上，ResGS 的 PSNR 相比原始 3D-GS 有显著提升，特别是在包含丰富纹理和高频细节的场景中（如 garden、bicycle 等）。SSIM 和 LPIPS 的改善同样明显，说明 ResGS 不仅在像素级误差上更优，在结构相似性和感知质量上也超越了现有方法。作者归因于残差分裂能够有效恢复原始 3D-GS 中因过度 split 而丢失的细节。

**泛化性验证**：ResGS 的 residual split 被应用于多种 3D-GS 变体，包括 Mip-Splatting、Gaussian-SLF 等，均在原有方法的基础上获得了额外的性能提升。这证明了 residual split 作为一种通用 densification 策略的有效性，不依赖于特定的 3D-GS 框架。

**训练效率**：由于残差分裂保留了父 Gaussian 的覆盖，ResGS 在训练过程中不需要频繁添加新 Gaussian 来填补空洞，因此在相同训练时间下，ResGS 的 Gaussian 数量更少但渲染质量更高。同时，金字塔渐进策略减少了早期训练阶段的无效计算，整体训练时间有所减少。

**细节恢复能力**：ResGS 在视觉质量上展现出更锐利的纹理和更完整几何，特别是在处理精细结构（如金属栅栏、织物纹理、树叶等高频细节）时，相比原始 3D-GS 和 competing methods，模糊现象明显减少，边缘更加清晰。

## 优缺点分析

**优点**：
1. **概念简洁但深刻**：残差分裂的"保留-添加"策略从根本上重新思考了 densification 的本质——不是替代而是补充。这种思路类似于残差网络中的 skip connection，在深度学习中被证明是极其有效的范式。
2. **解耦了覆盖与细节**：原始 3D-GS 的 split 同时承担几何覆盖和细节恢复双重职责，导致两者互相干扰。ResGS 将这两者分离：父 Gaussian 负责覆盖，子 Gaussian 负责细节。
3. **渐进式金字塔监督**：从全局到局部的优化顺序符合人类观察场景的自然方式，避免了早期训练中的局部最优和过度拟合。
4. **即插即用的通用性**：可以作为任何 3D-GS 变体的 densification 替换模块，实验已验证在多种方法上的有效性。
5. **计算效率高**：更少的冗余 Gaussian，更快的收敛，更少的训练时间。

**缺点**：
1. **Gaussian 数量控制不够精细**：虽然残差分裂减少了无效 split，但保留了父 Gaussian 意味着在某些区域可能同时存在多个尺度的 Gaussian 表达同一个语义内容，理论上存在冗余。论文中未详细分析最终 Gaussian 数量的分布情况。
2. **金字塔分辨率层级固定**：金字塔的层数和分辨率切换时机似乎是基于经验设定的，对于不同复杂度的场景可能需要自适应调整。
3. **未涉及动态场景**：与 [[3dgs-slam/DGS-SLAM]] 或 [[dynamic-gs/EAG3R]] 等动态 3DGS 方法结合的效果未验证。残差分裂在动态场景中可能面临父 Gaussian 过时的风险——当场景内容变化时，保留的粗粒度父 Gaussian 可能已经不再反映真实几何。
4. **对 SLAM 场景的适用性有限**：ResGS 面向离线 novel-view synthesis，其训练流程假设所有视角图像同时可用，与在线 SLAM 场景中增量式处理的范式差异较大。对于 SkelGS-SLAM 而言，残差分裂的思想可以参考，但需要重新设计增量式版本。

## 方法继承

- **前作**：[[3dgs-slam/GS-SLAM]]（原始 3D-GS 的 densification 策略：gradient-based split/clone）, [[mapping-reconstruction/ContextGS]]（基于 Context 的 Gaussian 管理）
- **后继/相关改进**：[[fastgs]]（FastGS 加速训练）、[[absgs]]（AbsGS 解决梯度碰撞问题）、[[3dgs-slam/GigaSLAM]]（大规模场景 densification）
- **方法学关联**：残差思想与 Mip-Splatting 中的 multi-resolution 概念相通；渐进式优化与 Mip-NeRF 的思想一致

## 相关笔记

- [[3dgs-slam/GS-SLAM]] — 3D-GS 的原始 densification 策略分析
- [[3dgs-slam/SplaTAM]] — silhouette-based Gaussian expansion
- [[3dgs-slam/GigaSLAM]] — 大规模场景 Gaussian 管理
- [[mapping-reconstruction/ContextGS]] — anchor-based Gaussian compression
- [[dynamic-gs/DGS-SLAM]] — 动态场景中的 Gaussian 密度控制
- [[3dgs-slam/VarSplat]] — 自适应 Gaussian 密度策略
- [[Categories/Mapping-Methods]] — 建图方法分类总览

## 所属分类

[[Categories/Mapping-Methods]] / 3DGS / Novel View Synthesis / Densification Strategy

> **与 SkelGS-SLAM 的关联**：ResGS 的残差分裂策略为 SkelGS-SLAM 中的 anchor-level Gaussian 管理提供了新思路。在 SkelGS-SLAM 中，每个 anchor 控制一组 Gaussian primitives，残差分裂的思想可以映射到"保留父 anchor 覆盖范围 + 新增子 anchor 捕获细节"的层级结构。这对于实现 LOD（Level of Detail）式的增量建图有直接参考价值。同时，ResGS 揭示的"覆盖-细节"矛盾在 SLAM 场景中同样存在：在线增量式场景中，如何在保持已有地图稳定性的同时添加新细节，与 ResGS 的核心问题高度一致。
