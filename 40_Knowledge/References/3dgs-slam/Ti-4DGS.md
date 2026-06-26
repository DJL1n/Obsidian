---
tags:
  - SLAM
  - 4DGS
  - dynamic-scene
  - Gaussian-Splatting
  - Temporally-Initialized
---
# Temporally Initialized 4D Gaussian Splatting

> arXiv:2404.09257 | 2024
> 代码：有（https://github.com/Hanyang-Li/MIT-GS-HuangGroup）

---

## 0. 一句话结论
将 3DGS 扩展到动态场景，通过时间初始化策略实现 4D Gaussian Splatting 的实时训练与渲染。

## 1. 核心问题
现有 4D Gaussian Splatting 方法（如 4DGS、DynamicGS）需要大量时间进行离线优化，且初始化策略忽略了时间维度，导致动态场景重建效率低下、训练收敛慢。

## 2. 核心方法
该论文提出一种 **Temporally Initialized 4DGS** 框架，核心创新在于将时间维度显式引入 Gaussian 初始化过程。具体 pipeline 如下：

1. **时间感知初始化**：与传统 3DGS 一次性初始化不同，该方法在多帧上依次初始化 Gaussian 集合。对于每一帧，通过 SFM/colmap 获得稀疏点云作为空间初始化，然后通过时间索引将同一点在不同时刻的 Gaussian 关联起来。

2. **4D Gaussian 表示**：每个 Gaussian 具有空间位置 μ、协方差 Σ、不透明度 α、颜色系数（球谐函数），以及时间相关的变形场（deformation field）。变形场通过 MLP 或时间多项式参数化，将静态 Gaussian 位置映射为时间依赖的 μ(t)。

3. **渐进式训练**：不是一次性优化所有帧，而是按时间顺序逐步引入新帧。先优化初始帧的 3DGS，然后扩展后续帧，利用时间连续性的先验加速收敛。

4. **时间一致性约束**：相邻帧之间的 Gaussian 变形受平滑约束，防止过度拟合单帧噪声，同时保持动态物体的运动连续性。

## 3. 实验结果
在动态场景数据集上，相比 4DGS，训练时间减少约 3-5 倍，渲染质量（PSNR/SSIM/LPIPS）接近或超越现有方法，支持实时或近实时渲染。

## 4. 与 SkelGS-SLAM 的关联
- **可借鉴点**：时间维度初始化策略可以迁移到 SkelGS-SLAM 的 anchor 生命周期管理——新 anchor 不应一次性全量创建，而应按时间渐进引入。时间连续性约束可以类比 SkelGS-SLAM 中 [[slam-frontends/patch-based/DPVO]] 前端的光流/运动一致性。
- **差异**：Ti-4DGS 是离线重建方法，非 SLAM 系统；无 pose estimation 回路。SkelGS-SLAM 需要在单目条件下在线完成 tracking + mapping。
- **融合方向**：SkelGS-SLAM 的 anchor-structured GS 可以借鉴时间初始化策略——当 CertifiedGeometryPacket 到达时，渐进式展开 anchor 下的 Gaussian 集合，而非一次性创建所有 Gaussians，从而减少不必要的渲染开销和内存占用。

## 相关笔记
- [[[[gs-slam/dynamic/4DGS-SLAM]]]] — 4D Gaussian Splatting SLAM 系统
- [[[[gs-slam/monocular/Gaussian-SLAM]]]] — RGB-D 下的 4D GS SLAM
- [[Dynamic-Scene-Handling]] — 动态场景处理策略对比

## 所属分类

[[Categories/4D-Gaussian]]
