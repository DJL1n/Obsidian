# DPVO Temporal Frontend Integration

## Goal

在 SkelGS-SLAM 系统中将 DPVO 作为 anchor temporal evidence 的主前端，利用其 patch lifecycle 信号驱动 anchor maturity 判断，而非直接使用 dense depth 或 pairwise matches。

## Background

DPVO 比 DROID 更轻（60-120 FPS, 29-57% memory）、比 MASt3R 更适合高频 tracking、比 LightGlue 提供完整 lifecycle。它最适合做 anchor temporal trust backbone。

## Integration pipeline

### Step 1: Patch diagnostics (shadow mode)
对每个 DPVO patch 记录：
- patch_id, source_frame, connected_frames
- center pixel, inverse_depth
- confidence Σ over edges
- trajectory revision δ over edges
- BA residual
- depth update magnitude
- survival length, visibility count
- nearby normal/depth agreement

### Step 2: Anchor score computation
AnchorScore = high confidence + long survival + low BA residual + small depth update + multi-view consistency + normal agreement - dynamic risk - free-space contradiction

### Step 3: Anchor admission gate
DPVO patch candidate → temporal gate → depth/normal gate → geometric gate → static support gate → CertifiedAnchor

### Step 4: ChildGS birth
CertifiedAnchor → local tangent frame → spawn child Gaussians → GS optimize appearance (geometry regularized/bounded)

## Key constraints

- 不要：DPVO patch depth → 直接 birth Gaussian
- 必须：DPVO evidence + depth/normal predictor + multi-view reprojection + free-space/surface-band gate → certified anchor

## Related notes

- [[DPVO]]
- [[Patch-Graph-VO]]
- [[Patch-Lifecycle]]
- [[ChildGS-Local-Derivation]]
