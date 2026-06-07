# SkelGS-SLAM — Decision Log

## 2026-06-07 — MASt3R-SLAM 论文分析结论

### 背景
完整阅读并整理了 MASt3R-SLAM (CVPR 2025) 论文，评估其作为 GS 前端的可行性。

### 关键判断

**MASt3R-SLAM 的定位：强 geometric proposal generator，不是 final certified GS geometry provider。**

#### 可采用的机制
1. **Ray residual** — GeometryPacket 的一致性应分为：
   - Strong term: angular / ray / bearing consistency
   - Weak term: depth / distance / scale consistency
   - Late term: GS rendering utility
   - 不要直接 3D point residual 主导 pose 或 anchor admission。

2. **Canonical pointmap fusion** — raw MASt3R packet → candidate packet → fused canonical packet → GS birth packet。GS 不应该直接吃 raw two-view output。

3. **Loop edge 需要 geometry verification** — retrieval candidate 不能直接成为约束。

4. **Scale/gauge 显式建模** — 不能只靠后处理 scalar。

#### 不建议照搬的
- MASt3R-SLAM 没有 full global geometry refinement
- 不输出 GS-ready uncertainty
- 不处理 dynamic ownership

### 对 DROID/DPVO 的定位
Temporal trust / lifecycle / uncertainty 更可能来自 DPVO/DROID-style signals，而非 MASt3R-SLAM。

### 后续方向
- 吸收 ray-consistency 和 canonical fusion 机制
- 保留 DPVO/DROID 的 temporal optimisation 优势
- 验证 anchor skeleton 中的 lifetime/uncertainty 信号来源

### 状态
- [x] Validated

---

### 相关笔记
- [[40_Knowledge/References/MASt3R-SLAM]]

---

## 2026-06-07 — DROID-SLAM 论文分析结论

### 背景
完整阅读并整理了 DROID-SLAM (NeurIPS 2021) 论文，评估其 anchor/packet coherence 方向的 Temporal signal 价值。

### 关键判断

**DROID-SLAM 的定位：temporal optimisation backbone，不是 final geometry oracle。**

#### 最值得挖的不是深度图，而是优化痕迹
DROID-SLAM 每条 graph edge 提供的大量信号：
- w_ij: per-pixel / per-edge confidence
- r_ij: 网络认为当前几何投影需要修正多少
- BA residual: corrected correspondence 与投影位置的差距
- Δd: depth update magnitude
- Δξ: pose update magnitude
- λ: damping / local depth instability signal
- frame graph edge survival: covisibility stability
- flow distance: baseline / redundancy / keyframe spacing signal

这些可以映射到 anchor 机制：
- **admission**: low residual, high confidence, repeated visibility, small update magnitude, stable depth over window
- **rejection**: high residual, low confidence, large recurrent revision, inconsistent depth update, unstable covisibility

#### DROID vs MASt3R 的定位

| | MASt3R-SLAM | DROID-SLAM | DPVO |
|---|---|---|---|
| 信息来源 | two-view 3D prior | dense correlation + recurrent flow | sparse patch correlation |
| 匹配方式 | ray-based iterative projective | correlation volume + recurrent revision | patch graph + differentable BA |
| 几何优化 | second-order keyframe graph | differentiable dense BA | patch-level BA |
| 内参需求 | uncalibrated (generic camera) | 需要已知内参 | 需要已知内参 |
| 强项 | wide-baseline, uncalibrated | temporal optimisation, pose-depth joint | 轻量、高帧率 |
| 对 anchor 价值 | 两视图几何初始化 | temporal trust signal 最丰富 | 高帧率 temporal evidence |

#### 不建议照搬的
- DROID depth 不自动解决 GS birth coherence（depth 为 pose/depth consistency 服务，非 surface 优化）
- DROID confidence 是 learned BA weight，非完整 uncertainty，需二次校准
- DROID 计算成本高（~11 GB），长序列 dense graph 可能成瓶颈

### 研究架构定位更新
- **DROID/DPVO**: 时序优化骨架和 trust signal
- **MASt3R**: 强 two-view / loop / wide-baseline geometry proposal
- **Depth-normal predictor**: 更平滑 metric-like dense geometry
- **CertifiedGeometryPacket / Anchor skeleton**: 负责把这些信号变成 GS 可消费的稳定结构

### 后续方向（更新）
- DROID/DPVO 作为主时间骨架，获取 temporal trust signal
- 结合 MASt3R 的 wide-baseline geometry proposal
- Anchor admission/rejection 规则基于 residual/confidence/update/survival 组合
- 最终 GS geometry 必须经过 certification，不能直接相信 DROID depth

### 状态
- [x] Validated

---

### 相关笔记
- [[40_Knowledge/References/DROID-SLAM]]
