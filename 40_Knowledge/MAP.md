# 🗺️ Knowledge Map

## 研究线程（核心组织）

### 📌 与你直接相关

| 线程 | 涉及论文 | 核心问题 |
|---|---|---|
| [[anchor-structured-gs]] | 6 篇 | anchor → Gaussian 表示 |
| [[feed-forward-3dgs]] | 12 篇 | 几何基础模型 + feed-forward 3DGS |
| [[uncertainty-aware-mapping]] | 2 篇 | per-primitive reliability |
| [[dynamic-scene-handling]] | 10 篇 | 动态场景处理 |

### 📌 基础工具

| 线程 | 涉及论文 | 说明 |
|---|---|---|
| [[vision-language-models]] | 4 篇 | CLIP, SAM, UNITER |

## 论文库导航

按技术域：

### SLAM / 3DGS
- [[3dgs-slam]] — 完整 GS-SLAM 系统（29 篇）
- [[slam-frontend]] — SLAM 前端：DPVO, DROID, FlashSLAM 等（7 篇）
- [[dynamic-gs]] — 动态场景 GS（7 篇）

### 几何基础模型
- [[geometry-model]] — DUSt3R, MASt3R, VGGT 等（7 篇）
- [[mapping-reconstruction]] — NeRF/TSDF/3DGS 建图（8 篇）
- [[matching-representation]] — 特征匹配（2 篇）
- [[semantic]] — 语义 GS（2 篇）

### 基础模型
- [[foundation-models]] — Attention, BERT, GAN, Diffusion 等（21 篇）

### 强化学习
- [[rl-dl]] — DQN, SAC, RLHF 等（10 篇）

### 多模态
- [[multimodal-sentiment]] — 情感分析（32 篇）

## 概念索引

### 核心概念
- [[anchor]] — anchor 定义、生成、可靠性
- [[dynamic]] — 动态场景、运动一致性
- [[geometry]] — 几何 prior、深度估计
- [[gs-primitive]] — Gaussian primitive 设计
- [[scale]] — 尺度一致性
- [[semantic]] — 语义分割
- [[submap]] — 子图管理
- [[tracking]] — 跟踪、patch 匹配

## 项目关联

- [[SkelGS-SLAM]] — 你的核心项目
- [[10_Projects/SkelGS-SLAM/INDEX]] — 项目索引
