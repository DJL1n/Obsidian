# Certified JDSA

## Goal

将 HI-SLAM2 的 JDSA scale alignment 扩展为多证据 Certified Scale Alignment：融合 depth prior + DPVO patch depth + MASt3R/DUSt3R pointmap + normal prior + GS rendered depth + anchor support，形成可认证的 scale/gauge 修正。

## Background

HI-SLAM2 的 JDSA 仅对齐 depth prior 与 BA depth。你的 monocular 系统还有更多证据源（DPVO temporal、MASt3R pairwise、SLAM3R window、CUT3R stateful），可以形成更强的 scale certification。

## Design

Evidence sources:
- Depth prior / predicted depth
- DPVO patch inverse depth + residual
- MASt3R/DUSt3R pointmap agreement
- Normal prior consistency
- GS rendered depth (gated)
- Anchor support history

Gate: scale correction accepted only if multi-source agreement > threshold. Reject if free-space violated / dynamic risk high / temporal residual high.

## Related notes

- [[HI-SLAM2]]
- [[JDSA-Scale-Alignment]]
- [[Gaussian-Map-Deformation]]
