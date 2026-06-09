# Certified Geometry Memory

## Goal

将 CUT3R 的 persistent latent state 思想显式化为可审计、可 gate、可回滚的 certified anchor / packet memory，作为 SkelGS-SLAM 的长期几何记忆。

## Design

| Component | Content |
|---|---|
| Recent candidate packets | Short-horizon DPVO/MASt3R/CUT3R/SLAM3R proposals |
| Mature certified anchors | Long-lived, temporal+geometric evidence gate passed |
| Long-term sparse anchor skeleton | Spann3R-style top-k retention, geometry-stable only |
| Geometry-version history | GO-SLAM-style: version changes when global BA updates pose/depth |
| Loop-affected packets | Loop closure → mark affected anchors for re-evaluation |

## Key principles

- Explicit: every entry has known support evidence
- Auditable: can trace which observations support which anchor
- Gateable: cannot enter memory without certification
- Rollbackable: if global correction invalidates an anchor, can quarantine

## Contrast

| | CUT3R persistent state | Certified Geometry Memory |
|---|---|---|
| State type | latent | explicit |
| Auditable | 否 | 是 |
| Gateable | 否 | 是 |
| Certifiable | 否 | 是 |

## Related notes

- [[CUT3R]]
- [[Persistent-State-Pointmap]]
- [[Retrieval-Guided-Registration]]
