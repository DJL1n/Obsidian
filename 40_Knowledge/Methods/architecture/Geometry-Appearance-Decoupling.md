# Geometry-Appearance Decoupling

## Goal

在 dense SLAM / neural implicit mapping 中，将 geometry 和 appearance 的特征表示分离，避免 appearance 的多变导致 geometry 遗忘或漂移。

## Background

ESLAM 的设计：geometry feature planes 负责 TSDF，appearance feature planes 负责 RGB。Appearance 受光照、曝光、视角、纹理影响更大，变化更频繁，如果与 geometry 共享同一套 feature 可能导致 geometry forgetting。

## Design

### 独立特征平面
- geometry: coarse (24 cm) + fine (6 cm) plane features
- appearance: coarse (24 cm) + fine (3 cm) plane features
- 各 32 channels

### 独立 decoder
- h_g: f_g(p) → TSDF ϕ_g(p)
- h_a: f_a(p) → raw RGB ϕ_a(p)

### 损失分离
- depth/TSDF loss → 只更新 geometry planes + h_g
- color loss → 主要更新 appearance planes + h_a
- (实际 ESLAM 中所有 loss 同时作用于所有参数，但特征平面本身已分离)

## 对 GS-SLAM 的借鉴

### 核心原则
不要让 color/render loss 过早支配 geometry birth。Geometry confidence 和 appearance quality 应该分离建模。

### 具体可迁移
- Anchor admission: 基于 geometry consistency (depth/normal/residual) 而非 render quality
- GS optimization: geometry (position, scale, rotation) 和 appearance (SH, opacity) 可以有不同更新策略
- 坏 geometry 不应靠 render loss 修复 — 应回到 geometry certification

## Related notes

- [[ESLAM]]
- [[Tri-Plane-Feature]]
- [[TSDF-Direct-Supervision]]
