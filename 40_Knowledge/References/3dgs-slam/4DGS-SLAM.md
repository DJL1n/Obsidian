---
tags:
  - SLAM
  - 4DGS
  - dynamic-SLAM
  - Gaussian-Splatting
  - RGB-D
---
# 4D Gaussian Splatting SLAM

> arXiv:2403.15624 | 2024
> 代码：有（https://github.com/fuqióliu/4D-Gaussian-Splatting-SLAM）

---

## 0. 一句话结论
首个将 4D Gaussian Splatting 与 RGB-D SLAM 结合的系统，实现动态场景下的实时建图与位姿估计。

## 1. 核心问题
现有 3DGS-SLAM（如 GS-SLAM、SplaTAM）假设场景是静态的，无法处理动态物体；而 4DGS 方法（如 4DGS）缺乏 SLAM 能力，无法在线估计相机位姿。

## 2. 核心方法
该论文构建了端到端的 **4DGS-SLAM** 系统，核心 pipeline 如下：

1. **4D Gaussian 表示**：每个 Gaussian 携带 3D 空间属性（位置 μ、协方差 Σ、不透明度 α、球谐颜色）加上时间维度。时间维度通过可学习的变形场（deformation field）实现——MLP 网络以静态位置 + 时间戳为输入，输出位移量，使得 Gaussian 可以随时间变化位置。

2. **Tracking-Mapping 循环**：
   - **Tracking 阶段**：利用当前帧 RGB-D 观测，从 4D Gaussian map 渲染 RGB 和深度图，通过可微 splatting 计算 photometric + depth loss 对相机位姿进行梯度优化。这是与 3DGS-SLAM 相同的核心机制，但渲染的是变形后的动态 Gaussian。
   - **Mapping 阶段**：自适应地添加、删除、优化 Gaussian。新增 Gaussian 通过 depth back-projection 初始化，删除机制基于 rendered depth 与观测 depth 的残差。变形场 MLP 在 mapping 阶段联合优化。

3. **时间索引管理**：系统维护滑动时间窗口，只对窗口内的帧进行变形场优化。超出窗口的 Gaussian 可以被冻结或移除，以控制计算和内存开销。

4. **动态-静态解耦**：通过变形场的幅度判断 Gaussian 属于静态还是动态——位移小的视为静态（直接参与 tracking），位移大的视为动态（需要变形场补偿）。

## 3. 实验结果
在动态 RGB-D 场景（如 TUM-RGBD、Dynamic Replica）上，相比静态 GS-SLAM 在动态区域有显著更低的跟踪误差和渲染伪影，PSNR 提升约 2-5dB，系统运行约 10-15 FPS。

## 4. 与 SkelGS-SLAM 的关联
- **可借鉴点**：
  1. 变形场思路可以转化为 SkelGS-SLAM 的 anchor motion model——anchor 本身不动，但 Gaussian 通过相对位移建模局部运动。
  2. Tracking-Mapping 循环中的可微 splatting tracking 是核心范式，SkelGS-SLAM 可以用类似机制做 map-based feedback，但 DPVO 仍为主前端。
  3. 动态-静态解判据（基于位移幅度）可用于 SkelGS-SLAM 的 geometry packet confidence 评分。
- **差异**：4DGS-SLAM 依赖 RGB-D depth，SkelGS-SLAM 是单目；4DGS-SLAM 的变形场 MLP 是隐式表示，SkelGS-SLAM 的 anchor 是显式结构化。
- **融合方向**：SkelGS-SLAM 可以考虑将 anchor-level deformation 作为轻量级动态建模模块，在 CertifiedGeometryPacket 中附加运动先验。不引入完整 MLP 变形场，而是用 per-anchor 的 affine motion model（6 参数），保持计算轻量化。

## 相关笔记
- [[3dgs-slam/Ti-4DGS]] — 时间初始化策略
- [[[[3dgs-slam/GS-SLAM]]]] — RGB-D 3DGS SLAM baseline
- [[[[dynamic-gs/CAD-SLAM]]]] — 动静解耦 SLAM
- [[Dynamic-Scene-Handling]] — 动态场景处理策略

## 所属分类

[[Categories/4D-Gaussian]]
