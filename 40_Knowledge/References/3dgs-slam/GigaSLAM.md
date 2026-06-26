# GigaSLAM: Large-Scale Monocular SLAM with Hierarchical Gaussian Splats

> arXiv:2403.07684 | 2024 (SIGGRAPH 2024)
> 代码：有（[GitHub](https://github.com/fuxiao0/GigaSLAM)）

## 0. 一句话结论
分层 GS 表示 + 局部 BA 实现大场景单目 SLAM，内存与速度解耦。

## 1. 核心问题
3DGS-based SLAM 方法（如 Nice-SLAM、Gaussian-SLAM）在大规模场景下面临两个核心瓶颈：（1）GS 数量随场景线性增长，内存消耗巨大；（2）全局优化成本过高，无法实时运行。GigaSLAM 问：能否通过分层的 GS 表示和局部化的优化策略，实现真正意义上的大场景单目 SLAM？

## 2. 核心方法
GigaSLAM 的核心创新在于**分层高斯 splatting**和**局部化优化**：
1. **Hierarchical Gaussian Splats**：GS 被组织为多层结构——底层是精细的、与帧关联的 GS（高频率细节），上层是粗粒度的、场景级共享的 GS（低频率几何）。这种分层让系统用更少的 GS 覆盖更大的空间。
2. **Local Window Optimization**：不是全局 BA，而是维护一个滑动窗口，仅优化窗口内的 GS 和位姿。窗口外的 GS 被压缩到上层表示，不参与在线优化。
3. **Coarse-to-Fine Mapping**：先用粗 GS 快速建立场景骨架（low-frequency geometry），再逐步 refine 细节。这与传统 SFM 的"先粗后细"哲学一致，但用 GS 实现。
4. **Incremental Culling & Merging**：通过聚类将相似 GS 合并，控制 GS 总数增长；不活跃的 GS 被提升到上层。

Pipeline：新帧 → DPVO/DSO 前端追踪 → 粗 GS 层匹配 → 精细 GS 层优化 → 窗口管理 + 合并/剔除 → 更新分层地图。

## 3. 实验结果
- 在大型室内场景（ScanNet、RE10K）上实现了实时单目 SLAM（~10-20 FPS）
- GS 数量比 Gaussian-SLAM 减少 10-50 倍，但重建质量相当或更优
- 轨迹精度在 TUM-RGBD 上与现有 GS-SLAM 方法持平
- 内存占用恒定（受窗口大小控制），可处理数千米级场景

## 4. 与 SkelGS-SLAM 的关联
- **可借鉴点**：分层 GS 设计是 SkelGS-SLAM 的 anchor 结构的自然推广——anchor 本身就是"粗层"，普通 GS 是"细层"。GigaSLAM 的窗口管理与合并策略可直接迁移
- **差异**：GigaSLAM 的分层是显式的 multi-resolution，SkelGS-SLAM 的 anchor 是语义/结构驱动的（基于场景几何）；GigaSLAM 无回环检测
- **融合方向**：将 GigaSLAM 的分层合并策略用于 SkelGS-SLAM——当 anchor 下的 GS 密度过高时，合并到 anchor 级别；窗口管理可解决 SkelGS-SLAM 的内存增长问题，使其扩展到大场景

## 相关笔记
- [[3dgs-slam/GigaSLAM]] 是第一个明确提出"大场景 [[3dgs-slam/GS-SLAM]]"问题的工作
- 分层设计与 anchor 结构高度同源，可作为 SkelGS-SLAM 的理论支撑
- 窗口管理是实时 SLAM 的关键，值得深入研究

## 方法继承

- **前作**：[[3dgs-slam/GS-SLAM]]（hierarchical GS）
- **后继**：[[MAGiC-SLAM]]

## 所属分类

[[Categories/Monocular-GS-SLAM]]
