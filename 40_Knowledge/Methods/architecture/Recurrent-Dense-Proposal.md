# Recurrent Dense Proposal

## Goal

在 SkelGS-SLAM 中将 CUT3R 作为 recurrent dense geometry witness：提供 persistent-state pointmap 作为候选几何证据源，与 DPVO temporal evidence、MASt3R pairwise evidence 一起进入 CertifiedGeometryPacket。

## Usage

- Role: candidate dense pointmap proposal source
- Not: system truth / direct GS birth source / DPVO replacement
- Fuse with: DPVO residual+survival + MASt3R agreement + depth-normal + free-space + global consistency

## Failure detection
If DPVO stable + CUT3R inconsistent → candidate risk ↑
If DPVO stable + MASt3R agrees + CUT3R agrees → strong support

## Related notes

- [[CUT3R]]
- [[Persistent-State-Pointmap]]
- [[Certified-Geometry-Memory]]
