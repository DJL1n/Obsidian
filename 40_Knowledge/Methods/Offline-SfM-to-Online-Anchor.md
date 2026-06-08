# Offline SfM to Online Anchor

## Goal

将 MASt3R-SfM 的 constrained pointmap + anchor depth + retrieval graph 思想迁移到在线 anchor skeleton 中。

## Migration

| MASt3R-SfM | Your system |
|---|---|
| Raw pointmap from MASt3R | MASt3R/DPVO/SLAM3R candidate |
| Constrained by camera model | Constrained by temporal + scale + normal + free-space |
| Anchor depth (pseudo-track) | Anchor support cell (depth + normal + evidence) |
| Offline retrieval graph | Online retrieval for loop/reloc/anchor reobservation |
| Coarse→fine 两阶段全局优化 | Progressive anchor admission (coarse support → fine detail) |

## Not for
Online tracking frontend replacement / real-time VO / dynamic modeling。

## Related notes

- [[MASt3R-SfM]]
- [[Constrained-Pointmap]]
- [[Anchor-Depth-Parameterization]]
