# Anchor-Structured Gaussian Splatting

## 核心问题
如何把 compact 3D 几何原型和 high-fidelity Gaussian rendering 结合起来，既不损失渲染质量也不爆炸存储？

## 演进线

```
Scaffold-GS (2024) → ContextGS (2024) → SEGS-SLAM (2025) → AnchorSplat (2026)
```

共同范式：**anchor 作为 compact 3D 原型，Gaussian 从 anchor 派生**。

## 论文群

### 起点
- [[matching-representation/Scaffold-GS]] — anchor → Gaussian 表示的开创性工作
- [[mapping-reconstruction/ContextGS]] — anchor 压缩，子图级别管理

### SLAM 分支
- [[semantic/SEGS-SLAM]] — 语义 + anchor GS SLAM
- [[3dgs-slam/GauS-SLAM]] — 2D Gaussian Surfel（类似思路）
- [[3dgs-slam/GS-SLAM]] — RGB-D GS-SLAM（free cloud）

### 最新进展
- [[3dgs-slam/AnchorSplat]] — 3D-first feed-forward，Gaussian 从 anchor 出生

## 与你的项目关联

你的 SkelGS-SLAM AnchorBank 融合了这条线的所有思想：
- 从 Scaffold-GS 学 anchor→Gaussian 的表示
- 从 ContextGS 学子图级别压缩
- 从 AnchorSplat 学 3D-first 理念

## 相关 Concepts

- [[anchor-structured-gs-paper-chain]] — 完整论文链分析
- [[Anchor定义和anchor生成结构]] — 你的 anchor 设计
- [[AnchorReliability-Admission-Gate]] — reliability gate

## 相关线程

- [[feed-forward-3dgs]] — 3DGS 表示的另一个方向
