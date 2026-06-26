# 方法继承关系图谱

## GS-SLAM 核心谱系

```
SplaTAM (2023, RGB-D, DROID-style tracking)
  → MonoGS (2024, 单目)
    → GS-SLAM (2024, 纯 monocular GS-SLAM)
      → GauS-SLAM (2D Gaussian Surfel)
      → Splat-SLAM (keyframe-level)
      → GigaSLAM (hierarchical GS)
        → MAGiC-SLAM (multi-agent)
      → SING3R-SLAM (submap-based indoor)
      → OpenMonoGS-SLAM (open-set semantic)
      → RTG-SLAM (compact lifecycle)
      → LVD-GS (LiDAR-Visual)
      → CAD-SLAM → 动态方向
      → VarSplat → Uncertainty 方向
```

## Anchor 表示谱系

```
Scaffold-GS (2024, anchor → Gaussian)
  → ContextGS (anchor compression)
    → SEGS-SLAM (semantic + anchor)
    → AnchorSplat (3D-first feed-forward)
```

## DPVO 谱系

```
DPVO (patch-based VO, 无地图)
  → DPV-SLAM (VO → SLAM, 加回环 + BA)
```

## 几何基础模型谱系（重点）

```
DUSt3R (2024, 两视图 pointmap regression, 开创性)
  → MASt3R (多视图扩展)
    → MASt3R-SfM (offline SfM pipeline)
    → MASt3R-SLAM (SLAM 版本)
    → VGGT (2025, CVPR best paper, 通用几何模型)
      → VGGT-SLAM (SLAM 版本)
  → Spann3R (空间记忆)
    → CUT3R (持续状态)
      → SLAM3R (retrieval-guided SLAM)
```

**这条线的核心思想**：用 feed-forward 方式直接从图像重建 3D 几何。DUSt3R 是起点，后续所有工作都在它的基础上扩展。

## 动态场景谱系

```
WildGS-SLAM (DINOv2 动态过滤)
  → DGS-SLAM (dynamic Gaussian)
    → ADD-SLAM (adaptive dynamic)
    → CAD-SLAM (consistency-aware, MASt3R prior)
      → Dy3DGS-SLAM (monocular dynamic)
```

## SLAM 前端谱系

```
DROID-SLAM (neural depth estimation)
  → DROID-SLAM 被大量 GS-SLAM 系统引用
GO-SLAM (GPU-optimized)
  → FlashSLAM (fast SLAM)
  → GSO-SLAM (EM coupling)
  → VPGS-SLAM (large-scale)
```

## 多传感器谱系

```
Gaussian-LIC (2025, LiDAR+IMU+Camera)
  → Gaussian-LIC2 (continuous-time, zero-shot depth)
```
