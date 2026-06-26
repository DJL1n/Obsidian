# Feed-Forward 3DGS & Geometric Foundation Models

## 核心问题
能否用一次 forward pass 直接从图像重建 3D 几何？替代 iterative optimization。

## 演进线

```
DUSt3R (2024) → MASt3R (2024) → Spann3R → CUT3R → SLAM3R → VGGT
```

## 论文群

### Foundation Models
- [[geometry-priors/feed-forward/DUSt3R]] — geometric 3D vision made easy
- [[geometry-priors/feed-forward/MASt3R]] — multi-view extension
- [[geometry-priors/feed-forward/MASt3R-SfM]] — structure-from-motion pipeline
- [[geometry-priors/feed-forward/Spann3R]] — spatial reasoning
- [[geometry-priors/feed-forward/CUT3R]] — continuous 3D perception
- [[geometry-priors/feed-forward/SLAM3R]] — SLAM with foundation models
- [[geometry-priors/grounded/VGGT]] — best paper, visual geometry generalization

### Feed-Forward Reconstruction
- [[geometry-priors/grounded/VGGT-SLAM]] — SLAM with VGGT priors
- [[gs-slam/monocular/GigaSLAM]] — large-scale monocular with hierarchical GS
- [[gs-slam/experimental/Continuous-3D-Perception]] — persistent state model

### 3D Priors for SLAM
- [[geometry-priors/slam-adapted/SCE-SLAM]] — scene coordinate embeddings
- [[slam-frontends/patch-based/DPVO]] — patch visual odometry（几何 prior）
- [[gs-slam/monocular/DPV-SLAM]] — DPVO + SLAM

## 与你的项目关联

你的 SkelGS-SLAM 考虑用 MASt3R 作为几何 prior 基座，后来转向了 AnchorGraph 中心。
这个线程里的 paper 是你"几何 prior"思路的源头。

## 相关 Concepts

- [[Foundation-Model]] tag 的论文

## 相关线程

- [[anchor-structured-gs]] — 表示层
- [[dynamic-scene-handling]] — 3D prior 可用于动态过滤
