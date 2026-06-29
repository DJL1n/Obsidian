---
title: "Any Resolution Any Geometry: From Multi-View To Multi-Patch"
tags:
  - paper
  - geometry-estimation
  - depth-estimation
  - normal-estimation
  - transformer
  - high-resolution
---

# Any Resolution Any Geometry: From Multi-View To Multi-Patch

## 基本信息
- 作者: Wenqing Cui, Zhenyu Li, Mykola Lavreniuk, Jian Shi, Ramzi Idoughi, Xiangjun Tang, Peter Wonka
- 年份: 2026
- 来源: arXiv (cs.CV)
- arXiv链接: https://arxiv.org/abs/2603.03026

## 核心贡献

Any Resolution Any Geometry（简称 URGT — Ultra Resolution Geometry Transformer）的核心贡献是将 **VGGT（Visual Geometry Grounded Transformer）从多视角输入范式扩展为多 patch 单视角输入范式**，从而实现了任意分辨率的联合深度与法线估计。传统几何估计方法（如 MASt3R、DUSt3R、VGGT）通常依赖多视角输入来推断 3D 几何，但在实际应用（如单目 SLAM、视频分析、高分辨率场景理解）中，往往只能获取单视角或受限视角的输入。URGT 的创新在于：它不需要多视角输入，而是将单张高分辨率图像分割为多个 patch，在共享的 Transformer backbone 中进行跨 patch 注意力计算，从而在保持全局几何一致性的同时实现超高分辨率的几何估计。

URGT 提出了两个关键技术：**多 patch Transformer 架构**和 **GridMix patch 采样策略**。多 patch Transformer 将高分辨率图像切分为多个 patch，每个 patch 配备来自预训练模型的粗略深度和法线先验（coarse priors），然后在单次前向传播中联合处理所有 patch 以输出精细的几何估计。跨 patch 注意力机制确保不同 patch 之间的几何信息能够传播和协调，避免了传统 patch 处理中常见的边界不连续问题。GridMix 则通过在训练过程中概率性地采样不同的网格配置（即不同的 patch 划分方式），强制模型学习对 patch 分割的鲁棒性，从而在推理时能够适应任意分辨率和 patch 划分。

在性能方面，URGT 在 UnrealStereo4K 数据集上取得了 state-of-the-art 结果：深度估计的 AbsRel 从 0.0582 降低到 0.0291（约 50% 的改进），RMSE 从 2.17 降低到 1.31，法线估计的平均角度误差（MAE）从 23.36° 降低到 18.51°。更重要的是，URGT 展示了强大的零样本泛化能力和跨域泛化能力，能够在训练时未见过的分辨率和场景类型上保持良好的性能。

## 方法概述

URGT 的方法框架围绕**多 patch Transformer 架构**构建，包含以下核心组件：

**多 patch Transformer 架构**：URGT 的核心思想是将一张高分辨率图像视为多个 patch 的集合，而非单一整体。具体流程如下：首先，将输入图像分割为 N×M 个 patch（网格大小可根据分辨率自适应调整）；其次，对每个 patch，利用预训练的几何估计模型（如 MonoDepth、NormalFlow 等）提取粗略的深度和法线先验，作为 patch-level 的初始几何信息；然后，将所有 patch 的视觉特征和几何先验输入到一个共享的 Transformer backbone 中，通过自注意力和跨 patch 注意力进行联合处理；最后，每个 patch 输出精细化的深度图和法线图，拼接成完整的超高分辨率几何估计。

跨 patch 注意力是 URGT 的关键创新之一。传统 patch 处理方法（如滑动窗口）将每个 patch 独立处理，导致 patch 边界处出现不连续性。URGT 的跨 patch 注意力允许每个 patch 与所有其他 patch 进行信息交换，从而在共享 backbone 中建立全局几何约束。例如，如果 patch A 和 patch B 在物理上相邻且属于同一平面，跨 patch 注意力会确保它们的深度估计在该平面上保持连续。这种全局一致性约束在不增加计算复杂度的前提下显著提升了估计质量。

**GridMix patch 采样策略**：URGT 在训练过程中引入了 GridMix 策略——在每个训练 batch 中，以一定的概率随机选择不同的 patch 网格配置。例如，对于 4K 输入图像，可能在某一轮使用 4×4 网格（每个 patch 较大），在另一轮使用 8×8 网格（每个 patch 较小）。这种策略迫使模型学习对不同 patch 粒度的鲁棒性，避免模型过度依赖特定的网格划分。GridMix 还增强了模型的泛化能力：在推理时，URGT 可以适应任意分辨率的输入，自动选择最佳的 patch 分割策略。

**粗略先预增强**：URGT 利用预训练单目深度估计模型和法线估计模型为每个 patch 提供初始几何先验。这些先验在以下两个方面发挥作用：一是作为 Transformer 的初始化条件，加速收敛并减少优化空间；二是作为跨 patch 注意力中的额外约束，确保 patch 间几何估计的一致性。URGT 不依赖这些先验的绝对准确性——跨 patch 注意力会在联合处理中纠正和细化先验信息。

**单视角设计**：与 MASt3R、DUSt3R、VGGT 等多视角几何基础模型不同，URGT 仅需要单张图像作为输入。这使得 URGT 可以直接应用于单目 SLAM 系统中的几何初始化、视频帧的逐帧几何估计、以及无法获取多视角信息的场景。单视角设计也降低了数据收集成本——不需要精心标注的多视角图像对。

**与 VGGT 的关系**：URGT 本质上是对 VGGT 架构的扩展和改造。VGGT 使用多视角输入，通过视觉-几何 grounded attention 联合处理多个视角的特征。URGT 保留了 VGGT 的核心 Transformer 架构和注意力机制，但将"多视角"替换为"多 patch"，将跨视角注意力替换为跨 patch 注意力。这种替换使得 VGGT 的强大几何推理能力能够应用于单视角高分辨率场景。

## 实验结果

URGT 的实验评估主要聚焦于深度估计和法线估计两个任务：

**UnrealStereo4K 基准**：URGT 在 UnrealStereo4K 数据集上取得了全面的 state-of-the-art 结果。深度估计方面，AbsRel 从现有方法的 0.0582 降低到 0.0291，RMSE 从 2.17 降低到 1.31，δ<1.25 的精度从约 85% 提升到约 95%。法线估计方面，平均角度误差（MAE）从 23.36° 降低到 18.51°。这些结果在 4K 分辨率下取得，展示了 URGT 在超高分辨率下的几何估计能力。

**零样本泛化**：URGT 在训练时未见过的分辨率和场景类型上展示了良好的泛化能力。例如，在训练时主要使用室内场景和特定分辨率，推理时在室外场景和更高分辨率上仍然能保持合理的精度。这归功于 GridMix 策略带来的网格鲁棒性和跨 patch 注意力带来的全局一致性。

**跨域泛化**：URGT 在跨数据集泛化实验中表现出色，在 KITTI、Middlebury 等标准深度估计基准上也取得了竞争力结果，尽管这些数据集不在训练集中。

**计算效率**：尽管处理超高分辨率输入，URGT 的推理时间仍然可控。Transformer 的并行计算特性使得 patch 数量的增加不会线性地增加计算时间，跨 patch 注意力的计算复杂度与 patch 数量成二次方关系，但通过高效的注意力实现（如 Flash Attention）可以有效控制。

**与多视角方法对比**：URGT 的单视角深度估计质量与部分多视角方法（如基于光流或 MVS 的方法）在特定场景下相当，特别是在纹理丰富、视差明显的区域。但在大范围视差遮挡和弱纹理区域，多视角方法仍然具有优势，这是单视角方法的固有局限。

## 优缺点分析

**优点**：
1. **超高分辨率支持**：URGT 突破了传统 Transformer 在高分辨率输入下的计算瓶颈，通过多 patch 策略实现了任意分辨率的几何估计。这对于需要在 4K 或更高分辨率下进行几何分析的应用至关重要。
2. **单视角设计**：不需要多视角输入，大幅降低了数据收集和应用部署的门槛。与 MASt3R、DUSt3R、VGGT 等多视角方法相比，URGT 更加通用和灵活。
3. **跨 patch 注意力**：解决了传统 patch 处理中的边界不连续问题，确保全局几何一致性。这是 URGT 超越简单 patch 拼接方法的核心原因。
4. **GridMix 策略**：增强了模型的网格鲁棒性和泛化能力，使得推理时的分辨率自适应成为可能。
5. **与几何基础模型生态兼容**：URGT 基于 VGGT 架构，可以利用现有的几何基础模型生态（如 DUSt3R 的预训练权重、MASt3R 的粗略先验等）。

**缺点**：
1. **单视角固有局限**：单视角深度估计本质上是一个病态问题，URGT 虽然通过跨 patch 注意力和粗略先验缓解了这一问题，但在弱纹理区域、大范围遮挡和极端视差场景中仍然存在局限。无法替代多视角方法的全局几何约束。
2. **跨 patch 注意力的计算复杂度**：跨 patch 注意力的计算复杂度随 patch 数量呈二次方增长，在极端高分辨率下可能需要更高效的注意力机制（如稀疏注意力或层次注意力）。
3. **依赖预训练先验**：URGT 需要预训练的单目深度和法线模型提供初始先验，这在一定程度上限制了模型的自主学习能力。如果预训练先验在特定领域表现不佳，URGT 的性能也会受到影响。
4. **未验证与 3D-GS 的集成**：URGT 的输出（高分辨率深度和法线）可以直接用作 3D-GS 的几何先验，但论文中未探讨这一集成路径。将 URGT 与 3D-GS 结合可能是未来有趣的方向。

## 方法继承

- **前作**：[[geometry-model/VGGT]]（VGGT — 视觉几何 grounded Transformer）、[[geometry-model/MASt3R]]（MASt3R — 多视角匹配与重建）、[[geometry-model/DUSt3R]]（DUSt3R — 场景基础模型）
- **后继/相关改进**：将 URGT 的单视角几何估计与 3D-GS 的几何初始化结合，可为 SkelGS-SLAM 提供高精度的单帧几何先验
- **方法学关联**：多 patch Transformer 与 DETR 的 multi-scale 设计相通；GridMix 与 MixUp/CutMix 数据增强策略类似

## 相关笔记

- [[geometry-model/VGGT]] — VGGT 多视角几何估计，URGT 的基础架构
- [[geometry-model/MASt3R]] — MASt3R 多视角匹配与重建
- [[geometry-model/DUSt3R]] — DUSt3R 场景基础模型
- [[geometry-model/SLAM3R]] — 基于基础模型的 SLAM 前端
- [[slam-frontend/MASt3R-SLAM]] — MASt3R 在 SLAM 中的应用
- [[3dgs-slam/GS-SLAM]] — 3D-GS 的几何初始化可受益于 URGT 的深度先验
- [[mapping-reconstruction/resgs]] — ResGS 的几何恢复可用 URGT 提供初始几何框架
- [[Categories/Geometric-Foundation-Models]] — 几何基础模型分类

## 所属分类

[[Categories/Geometric-Foundation-Models]] / Geometry Estimation / Depth & Normal / Transformer / Single-view

> **与 SkelGS-SLAM 的关联**：URGT 为 SkelGS-SLAM 提供了一种潜在的**单视角几何初始化方案**。在 SkelGS-SLAM 中，CertifiedGeometryPacket 需要可靠的几何信息来初始化 Gaussian anchors。URGT 的高分辨率深度和法线估计可以直接作为单帧几何先验，为 anchor 的初始位置、尺度和朝向提供约束。特别是在 monocular 模式下（无 depth sensor），URGT 可以作为 CertifiedGeometryPacket 的几何信息来源，替代或补充传统的 SfM 方法。此外，URGT 的跨 patch 注意力机制提供了全局几何一致性约束，这与 SkelGS-SLAM 中 anchor 间几何一致性的目标高度一致。将 URGT 集成到 SkelGS-SLAM 的 pipeline 中，可能显著提升 monocular 模式下的建图精度和鲁棒性。
