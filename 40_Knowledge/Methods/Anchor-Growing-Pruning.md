# Anchor Growing-Pruning (Evidence-Gated)

## Goal

在结构化的 anchor scaffold 中，根据多视角几何证据而非纯渲染梯度来控制 anchor 的生长和剪枝，确保 anchor 只在几何可信、observations 充分的区域添加/保留。

## Background

Scaffold-GS 的 growing 基于 rendering gradient，pruning 基于 opacity contribution。对 SLAM/geometry packet 来说，这不够 — 需要加入 temporal、geometric、consistency gate。

## Procedure

### Growing gate
candidate region can grow anchor only if ALL:
1. temporal observation count ≥ threshold
2. DPVO/DROID residual stable
3. depth/normal predictor agrees across views
4. local reprojection residual low
5. dynamic risk low
6. no strong free-space contradiction
7. render gradient high (auxiliary, not primary)

### Pruning gate
anchor should be pruned only if:
- opacity contribution persistently low
AND at least one of:
- observation count insufficient
- residual history increasing
- visibility inconsistent
- geometry support weak
- map overlap redundant

### Observation threshold
- 只有被访问足够多次的 anchors 才允许 growing/pruning
- 避免短期单次观测导致的误操作

## Contrast with Scaffold-GS

| | Scaffold-GS | Evidence-gated (yours) |
|---|---|---|
| Growing signal | rendering gradient | temporal + geometric + gradient |
| Pruning signal | opacity | opacity + observation + residual |
| Observation gate | N × ratio | temporal + multi-view consistency |
| Dynamic handling | 无 | dynamic risk score |

## Related notes

- [[Scaffold-GS]]
- [[Anchor-Scaffold-Representation]]
- [[ChildGS-Local-Derivation]]
