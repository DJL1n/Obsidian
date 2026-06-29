---
title: "FastGS: Training 3D Gaussian Splatting in 100 Seconds"
tags:
  - paper
  - 3DGS
  - acceleration
  - training-speed
  - mapping-reconstruction
---

# FastGS: Training 3D Gaussian Splatting in 100 Seconds

## 基本信息
- 作者: Shiwei Ren, Tianci Wen, Yongchun Fang, Biao Lu
- 年份: 2025
- 来源: arXiv (cs.CV)
- arXiv链接: https://arxiv.org/abs/2511.04283

## 核心贡献

FastGS 的核心贡献是提出了一种**基于多视角一致性（multi-view consistency）的加速框架**，将 3D Gaussian Splatting 的训练时间从传统的数十分钟压缩到约 100 秒，同时保持与 state-of-the-art 方法相当甚至更优的渲染质量。FastGS 团队对现有 3D-GS 加速方法进行了系统性分析，发现它们的一个共同缺陷：**未能有效调节训练过程中 Gaussian 的数量**。具体而言，大多数加速方法通过减少迭代次数、跳过某些计算或降低分辨率来加快速度，但训练过程中 Gaussian 数量仍然不受控地增长，导致大量计算资源被浪费在冗余 Gaussian 的优化上。

FastGS 提出了一个全新的 densification 和 pruning 策略，完全摒弃了传统方法中的 budgeting 机制（如 DashGaussian 的显式 Gaussian 数量预算）。取而代之的是，FastGS 通过**多视角一致性评分**来评估每个 Gaussian 的重要性：如果一个 Gaussian 在所有可见视角下都贡献了稳定的、一致的渲染结果，则被认为是重要的、值得保留的；反之，如果在某些视角下渲染结果不一致或存在明显误差，则该 Gaussian 可能是冗余的或尚未充分优化的，应当被合并或剔除。

这一核心思想的精妙之处在于：多视角一致性自然地编码了 3D 场景的几何约束。在一个良构的 3D 场景中，同一物理位置从不同角度观察应该产生一致的渲染结果。因此，多视角一致性评分可以直接反映 Gaussian 是否正确地拟合了场景几何——这比传统的梯度阈值、不透明度阈值等指标更能区分"有用"和"冗余"的 Gaussian。

FastGS 在 Mip-NeRF 360、Tanks & Temples 和 Deep Blending 数据集上展示了显著的训练加速：相比 DashGaussian 达到 3.32 倍的加速，相比原始 3D-GS 在 Deep Blending 上达到 15.45 倍的加速。更重要的是，FastGS 的加速能力在不同任务上展现出强大的泛化性，包括动态场景重建、表面重建、稀疏视角重建、大规模重建和 SLAM 等场景，均能带来 2-7 倍的训练加速。

## 方法概述

FastGS 的方法框架围绕**多视角一致性（Multi-view Consistency, MVC）**构建，包含三个核心模块：**MVC 重要性评分**、**MVC 引导的 densification 策略**，以及**MVC 引导的 pruning 策略**。

**MVC 重要性评分**：对于场景中的每个 Gaussian，FastGS 在所有能够看到该 Gaussian 的相机视角下计算其渲染贡献的一致性。具体实现上，FastGS 对每个 Gaussian 在多个视角下的渲染结果进行比对，计算跨视角的渲染误差方差。方差越低，说明该 Gaussian 在不同视角下的表现越一致，越可能是场景的真实几何组成部分。方差越高，说明该 Gaussian 在不同视角下的表现不一致，可能是由于拟合了噪声、过拟合了特定视角，或本身就是冗余的。这个评分机制不需要额外的网络或预训练模型，完全基于 3D-GS 的渲染管线本身，计算开销极小。

**MVC 引导的 densification**：与传统方法对所有超过梯度阈值的 Gaussian 进行 clone/split 不同，FastGS 仅对多视角一致性评分高的 Gaussian 进行 densification。直觉是：一致性高的 Gaussian 已经建立了稳定的 3D 几何表示，对其进行细化可以获得真正的细节增益；一致性低的 Gaussian 尚未形成稳定的几何表示，对其进行细化只是将噪声扩散到更多 Gaussian 上。FastGS 通过设置一个自适应的一致性阈值来控制 densification 的范围，该阈值随训练进展自动调整——训练早期阈值较低以快速建立几何框架，训练后期阈值升高以精细控制新增 Gaussian 的数量。

**MVC 引导的 pruning**：FastGS 的 pruning 策略与 densification 紧密耦合。在多视角一致性评分低于某个阈值的 Gaussian 中，FastGS 根据其尺度、不透明度和渲染贡献进行综合评估，决定是缩小其不透明度（逐步淘汰）还是直接删除。关键创新在于，FastGS 的 pruning 不是基于单一指标（如不透明度或梯度大小），而是基于多视角一致性评分与渲染贡献的综合权衡：即使一个 Gaussian 的渲染贡献很小，如果它在多个视角下表现一致，FastGS 也会保留它，因为它可能是场景的真实但微弱几何特征。

**无需预算机制**：与 DashGaussian 等需要预设 Gaussian 数量上限的方法不同，FastGS 完全依赖 MVC 评分自适应地调节 Gaussian 数量。这意味着 FastGS 在不同复杂度场景下的 Gaussian 数量会自然收敛到合理的水平，无需手动调参。

**跨任务泛化**：FastGS 的核心 MVC 模块可以作为 drop-in 替换任何 3D-GS 方法中的 densification/pruning 模块。论文展示了在动态场景重建（如 Dynamic 3D-GS）、表面重建（如 SuGaR）、稀疏视角重建（如 SparseGS）、大规模场景重建和 SLAM 系统中的应用，均在保持各自方法特色的同时获得了显著的训练加速。

## 实验结果

FastGS 的实验评估覆盖了多个维度和数据集：

**训练速度**：在 Mip-NeRF 360 数据集上，FastGS 相比 DashGaussian 实现了 3.32 倍的训练加速，同时保持了相当的渲染质量（PSNR 差距在 0.1-0.3 dB 以内）。在 Deep Blending 数据集上，相比原始 3D-GS 实现了惊人的 15.45 倍加速，将训练时间从约 25 分钟缩短到约 100 秒。Tanks & Temples 数据集上的加速比介于两者之间。

**渲染质量**：尽管训练时间大幅缩短，FastGS 在 PSNR、SSIM 和 LPIPS 三个指标上与 DashGaussian 相当，在部分场景上甚至更优。这表明 FastGS 的 MVC 策略不仅没有损害质量，反而通过更精确的 Gaussian 选择提升了渲染效果。特别是在纹理丰富、细节复杂的场景中，FastGS 能够避免 DashGaussian 中因预算限制而过早停止 densification 导致的细节丢失。

**Gaussian 数量**：FastGS 在相同质量水平下使用的 Gaussian 数量显著少于原始 3D-GS 和 DashGaussian。在 Mip-NeRF 360 的某些场景中，Gaussian 数量减少了 40-60%，这直接对应于更少的内存占用和更快的渲染速度。

**跨任务泛化**：FastGS 作为 densification 模块集成到多种 3D-GS 变体中：在动态场景重建中加速了 2-3 倍，在表面重建中加速了 3-4 倍，在稀疏视角重建中加速了 2-5 倍，在 SLAM 系统中加速了 2-7 倍。这些结果验证了 MVC 策略的通用性——不论基础方法如何，基于多视角一致性的 Gaussian 管理都能显著减少冗余计算。

**消融实验**：FastGS 对 MVC 评分的计算方式、阈值自适应策略、densification/pruning 的耦合方式等进行了系统消融。结果表明，多视角一致性评分相比单一视角评分带来了最大的加速效果，而自适应阈值策略相比固定阈值在质量和速度之间取得了更好的平衡。

## 优缺点分析

**优点**：
1. **加速效果显著**：100 秒训练时间在保持质量的前提下是一个巨大的突破，对于快速原型验证、在线重建和大规模部署有直接价值。
2. **MVC 策略概念优雅**：多视角一致性是 3D 几何的固有属性，将其作为 Gaussian 管理的核心准则既直观又有效，不需要额外的学习组件或预训练模型。
3. **无需预算参数**：摆脱了 Gaussian 数量上限的手动设定，自适应调节使得方法在不同场景下都能工作，降低了使用门槛。
4. **泛化性极强**：2-7 倍加速覆盖动态重建、表面重建、稀疏视角、大规模场景和 SLAM 等多种任务，证明 MVC 是 3D-GS 领域的基础设施级改进。
5. **实现简单**：MVC 评分可以直接在现有 3D-GS 渲染管线中计算，代码改动量小，易于集成。

**缺点**：
1. **多视角计算开销**：MVC 评分需要在多个视角下对每个 Gaussian 进行渲染评估，这在 Gaussian 数量较多时可能带来显著的计算负担。论文中未详细分析 MVC 评分本身的计算时间占比，在极端大规模场景下可能需要优化。
2. **视角覆盖不均匀场景**：在视角覆盖不均匀的场景中（如某些区域只有少数视角可见），MVC 评分可能无法准确反映 Gaussian 的重要性——某些 Gaussian 可能因为视角覆盖少而被错误地认为一致性低。对于 SLAM 场景中的单目或部分 RGB-D 系统，视角覆盖通常不均匀，这可能导致 MVC 评分偏差。
3. **动态场景的一致性定义**：在动态场景中，同一物理位置在不同时间点的渲染结果可能因物体运动而不同，MVC 评分需要区分"因运动导致的不一致"和"因噪声导致的不一致"。论文中展示了在动态场景的加速效果，但未深入讨论这一区分。
4. **与在线 SLAM 的兼容性**：FastGS 假设所有视角图像同时可用以计算 MVC 评分，这在线下 novel-view synthesis 中是合理的，但在在线 SLAM 中，每帧到达时只能基于历史帧计算 MVC，新区域的 MVC 评分可能需要更长时间才能稳定。

## 方法继承

- **前作**：原始 3D-GS（Kerbl et al.）的 densification 策略、DashGaussian（budget-based Gaussian 管理）、Mip-Splatting（多尺度 Gaussian）
- **后继/相关改进**：[[resgs]]（ResGS 残差分裂改进 densification）、[[absgs]]（AbsGS 梯度碰撞问题）、[[3dgs-slam/GigaSLAM]]（大规模场景 Gaussian 管理）
- **方法学关联**：多视角一致性与多视图立体视觉（MVS）中的视差一致性概念相通；自适应阈值与 SLAM 中的动态特征选择策略类似

## 相关笔记

- [[3dgs-slam/GS-SLAM]] — 3D-GS SLAM 基础，在线 densification 的参考
- [[mapping-reconstruction/resgs]] — ResGS 残差分裂，互补的 densification 改进方向
- [[mapping-reconstruction/absgs]] — AbsGS 解决梯度碰撞，与 FastGS 的 pruning 目标一致
- [[3dgs-slam/GigaSLAM]] — 大规模场景下的 Gaussian 管理挑战
- [[3dgs-slam/VarSplat]] — 自适应 Gaussian 密度策略，与 MVC 有相似动机
- [[3dgs-slam/SplaTAM]] — silhouette-based expansion 可结合 MVC 进行更精细管理
- [[Categories/Mapping-Methods]] — 建图方法分类总览

## 所属分类

[[Categories/Mapping-Methods]] / 3DGS / Training Acceleration / Multi-view Consistency

> **与 SkelGS-SLAM 的关联**：FastGS 的多视角一致性评分为 SkelGS-SLAM 中的 anchor 质量评估提供了直接参考。在 SkelGS-SLAM 中，每个 anchor 控制一组 Gaussian，其质量评估需要从多个视角的渲染一致性出发。FastGS 的 MVC 评分可以作为 CertifiedGeometryPacket 验证机制的补充：如果一组 Gaussian 在多视角下渲染一致，则更有可能是可靠的几何信息。此外，FastGS 展示的 2-7 倍 SLAM 加速潜力对于 SkelGS-SLAM 的实时性目标至关重要——如何在保持前端 tracking 速率的同时加速后端 mapping 是系统设计的关键。
