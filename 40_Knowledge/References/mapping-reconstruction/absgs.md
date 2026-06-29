---
title: "AbsGS: Recovering Fine Details for 3D Gaussian Splatting"
tags:
  - paper
  - 3DGS
  - densification
  - gradient-collision
  - detail-recovery
---

# AbsGS: Recovering Fine Details for 3D Gaussian Splatting

## 基本信息
- 作者: Zongxin Ye, Wenyu Li, Sidun Liu, Peng Qiao, Yong Dou
- 年份: 2024
- 来源: arXiv (cs.CV)
- arXiv链接: https://arxiv.org/abs/2404.10484

## 核心贡献

AbsGS 的核心贡献是对 3D Gaussian Splatting（3D-GS）中**自适应密度控制策略的深层缺陷**进行了系统性分析和修复。论文首先指出，尽管 3D-GS 在 novel-view synthesis 领域取得了突破性成功，但在处理包含丰富高频细节的复杂场景时，经常出现过重建（over-reconstruction）问题：渲染图像在细节区域出现模糊、伪影或纹理丢失。AbsGS 团队深入挖掘了这一问题的根本原因，将其归结为**梯度碰撞（gradient collision）**现象。

**梯度碰撞**是指在过重建区域，多个 Gaussian 重叠覆盖了同一片像素区域，它们在反向传播时产生的梯度方向相反或相互抵消，导致原本应该被分裂的大 Gaussian 无法收到足够大的梯度信号来触发 split 操作。具体来说，当一个大 Gaussian 覆盖的区域已经包含多个小 Gaussian（之前 split 产生的），这些小 Gaussian 负责拟合该区域的高频变化，而大 Gaussian 的梯度被小 Gaussian 的梯度"淹没"或"抵消"。结果是：大 Gaussian 的梯度幅度看起来很小（因为大部分误差已经被小 Gaussian 吸收），所以不会被选中进行 split，但实际上它仍然覆盖了一个需要更细致建模的区域。这种"看起来不需要分裂，实际上需要分裂"的情况就是梯度碰撞。

AbsGS 提出了解决方案：**同向视空间位置梯度（homodirectional view-space positional gradient）**作为新的 densification 判据。与原始 3D-GS 使用梯度绝对值不同，AbsGS 分析梯度在视空间中的方向一致性——当多个重叠 Gaussian 在视空间中产生同向梯度时，说明它们都在尝试拟合同一个方向上的误差，此时应当触发 densification 来增加分辨率；当梯度方向不一致时，说明这些 Gaussian 已经在拟合不同的特征，此时不需要额外的 split。

这一分析框架不仅解释了 3D-GS 在复杂场景中的失败模式，还为 densification 策略的设计提供了理论基础。AbsGS 的改进被证明易于实现，可以即插即用地集成到各种 3D-GS 变体中，且在渲染质量提升的同时保持了相似的内存消耗。

## 方法概述

AbsGS 的方法围绕**梯度碰撞分析**和**同向视空间位置梯度**展开，包含以下几个关键组件：

**梯度碰撞诊断**：AbsGS 首先通过可视化实验展示了梯度碰撞的存在。作者在过重建区域选取典型的大 Gaussian，追踪其在训练过程中的梯度变化，发现这些 Gaussian 的梯度在训练中期就已经变得很小，导致它们被错误地判定为"已充分优化"而不再分裂。进一步的实验表明，这种现象在高密度区域（如纹理丰富的墙面、精细的栅栏、密集的树叶）尤为严重，这些区域正是 3D-GS 最需要高分辨率建模的地方。

**同向视空间位置梯度**：AbsGS 提出的核心创新是将 3D-GS 的 densification 判据从"梯度绝对值超过阈值"改为"同向视空间位置梯度的绝对值超过阈值"。具体实现如下：

1. 在每次渲染时，记录每个 Gaussian 在视空间（view-space）中的位置梯度 ∂L/∂p（p 为 Gaussian 在视空间中的投影位置）。
2. 对于同一像素区域的重叠 Gaussian，计算它们的位置梯度之间的方向一致性。如果两个 Gaussian 的位置梯度方向相同（点积为正），说明它们在试图减少同一方向的误差。
3. 将所有同向梯度累加得到"同向视空间位置梯度"，用其绝对值作为 densification 判据。
4. 同向梯度绝对值超过阈值的 Gaussian 被选中进行 split。

这一策略的关键直觉是：在过重建区域，大 Gaussian 的梯度被小 Gaussian 的梯度抵消，但它们的同向分量仍然存在。通过累加同向分量，AbsGS 能够"穿透"梯度碰撞，识别出真正需要分裂的大 Gaussian。

**实现细节**：AbsGS 对原始 3D-GS 的代码改动极小，主要是在 densification 逻辑中将梯度计算从 ∂L/∂μ（世界空间位置梯度）替换为同向视空间位置梯度。由于视空间投影在 3D-GS 的渲染管线中已经计算过，同向梯度的计算几乎没有额外开销。AbsGS 还支持对 clone 和 split 操作的联合优化，确保 densification 过程中 Gaussian 数量的合理增长。

**与其他改进的兼容性**：AbsGS 被验证可以与 Mip-Splatting、Gaussian-SLF、2DGS 等多种 3D-GS 变体结合使用，在各自的基础上进一步提升了细节恢复能力。这表明 AbsGS 的梯度碰撞分析是 3D-GS 的一个普遍性问题，而非特定实现的 bug。

## 实验结果

AbsGS 在多个基准数据集上进行了评估，主要包括 Mip-NeRF 360、Tanks & Temples 和 Deep Blending：

**渲染质量提升**：AbsGS 在所有数据集上均超越了原始 3D-GS 和其他 competing methods。在 Mip-NeRF 360 上，PSNR 提升约 0.5-1.0 dB，SSIM 和 LPIPS 也有明显改善。提升幅度在细节丰富的场景中更大（如 garden、bicycle），这与梯度碰撞在高频区域的严重性一致。

**内存消耗**：AbsGS 的 Gaussian 数量与原始 3D-GS 相当或略少，内存消耗保持在相似水平。这是因为 AbsGS 通过更精确的 split 策略避免了无效 densification——那些因梯度碰撞而本不该 split 的 Gaussian 被正确跳过，同时那些因梯度碰撞而被遗漏的大 Gaussian 得到了正确的 split。

**视觉质量**：定性对比显示，AbsGS 在精细结构（如铁艺栅栏、布料纹理、植物叶片）上的渲染明显更锐利，模糊现象显著减少。特别是在远距离、高分辨率渲染时，AbsGS 的细节优势更加明显。

**跨方法验证**：AbsGS 集成到 Mip-Splatting、Gaussian-SLF 等方法后，均在原有基础上获得了额外的 PSNR 提升（约 0.2-0.5 dB），证明了梯度碰撞是跨方法的普遍问题。

**消融实验**：AbsGS 对同向梯度的计算方式（绝对值 vs. 方向加权）、阈值策略（固定 vs. 自适应）、适用范围（仅位置梯度 vs. 所有参数梯度）进行了系统消融。结果表明，同向视空间位置梯度是最有效的判据，仅使用位置梯度就已经能捕获梯度碰撞的核心问题。

## 优缺点分析

**优点**：
1. **问题诊断深刻**：梯度碰撞是 3D-GS densification 中一个长期存在但未被充分理解的 issue。AbsGS 不仅修复了它，还提供了一个理论框架来解释为什么原始方法会在某些场景下失败。这种"先分析后解决"的方法论值得借鉴。
2. **实现极简**：同向视空间位置梯度的计算几乎没有额外开销，代码改动极小，易于集成到现有 pipeline。
3. **兼容性强**：作为 densification 判据的替换，AbsGS 可以与任何 3D-GS 变体结合，不限于特定框架。
4. **内存友好**：不增加 Gaussian 数量，反而可能减少无效 split，整体内存消耗与原始方法相当。
5. **与 ResGS 互补**：ResGS 从"残差分裂"角度改进 densification 的操作方式，AbsGS 从"梯度判据"角度改进 densification 的选择标准。两者可以结合使用，在更精确的 split 判据上执行更合理的 split 操作。

**缺点**：
1. **梯度的数值稳定性**：同向梯度的计算依赖于准确的梯度估计，在训练早期或某些极端场景下（如大面积纯色区域），梯度可能不稳定或接近零，导致 densification 决策不准确。
2. **未处理 clone 操作的梯度碰撞**：AbsGS 主要关注 split 操作的梯度碰撞，clone 操作（对高梯度的 Gaussian 进行复制）中是否也存在类似的梯度冲突问题未被深入分析。
3. **对 pruning 的影响有限**：AbsGS 聚焦于 densification 的选择标准，对 pruning（Gaussian 删除）策略的改进有限。在实际训练中，densification 和 pruning 是紧密耦合的，单方面改进可能不是最优解。
4. **SLAM 场景的适用性未验证**：AbsGS 面向 offline novel-view synthesis，其训练过程中所有视角同时可用的假设在在线 SLAM 中不成立。SLAM 场景中的梯度碰撞可能更加复杂，因为 Gaussian 是在增量式场景中不断添加和更新的。

## 方法继承

- **前作**：原始 3D-GS（Kerbl et al.）的 gradient-based densification、[[resgs]]（ResGS 残差分裂，解决同一问题的不同角度）
- **后继/相关改进**：[[fastgs]]（FastGS 的 MVC 评分也可以作为 densification 判据）、[[3dgs-slam/GigaSLAM]]（大规模场景 densification）
- **方法学关联**：梯度碰撞分析与 NeRF 中的 aliasing 问题概念相通；同向梯度思想与多模态融合中的 attention 机制类似

## 相关笔记

- [[mapping-reconstruction/resgs]] — ResGS 残差分裂，互补的 densification 改进
- [[mapping-reconstruction/fastgs]] — FastGS MVC 评分，可替代梯度作为 densification 判据
- [[3dgs-slam/GS-SLAM]] — 3D-GS 的原始 densification 策略
- [[3dgs-slam/VarSplat]] — 自适应 Gaussian 密度策略
- [[3dgs-slam/SplaTAM]] — RGB-D 条件下的 Gaussian 管理
- [[3dgs-slam/GigaSLAM]] — 大规模场景 Gaussian 管理
- [[mapping-reconstruction/ContextGS]] — anchor-based Gaussian 管理
- [[Categories/Mapping-Methods]] — 建图方法分类总览

## 所属分类

[[Categories/Mapping-Methods]] / 3DGS / Densification Strategy / Gradient Analysis

> **与 SkelGS-SLAM 的关联**：AbsGS 揭示的梯度碰撞问题在 SkelGS-SLAM 中同样存在。在 SkelGS-SLAM 的增量式建图中，新 anchor 引入的 Gaussian 与已有 anchor 的 Gaussian 可能存在空间重叠，导致梯度冲突。AbsGS 的同向视空间位置梯度可以作为 SkelGS-SLAM 中 anchor 级别的 densification 判据，确保只有真正需要细化的 anchor 才被扩展。此外，AbsGS 对"过重建"问题的分析提醒我们，在 SkelGS-SLAM 中，过度自信的 CertifiedGeometryPacket 可能导致不必要的 Gaussian 增殖，需要在 geometry gate 中加入更精细的判断机制。
