# CoGS-SLAM Architecture

## Goal

Multi-agent collaborative GS-SLAM: centralized or distributed, local GS submap + global alignment + communication-efficient fusion。

## Components

- Each agent: tracking + local mapping + local GS submap + semantic extraction + loop detection
- Server (centralized) or peers (distributed): inter-agent loop + submap merging + global GS refinement + PGO + semantic aggregation
- Communication: compressed summary / descriptor / low-opacity pruned GS / neural params
- Fusion: coarse geometric alignment → fine photometric/radiance refinement → semantic consistency check

## Related notes

- [[CoGS-SLAM-Survey]]
- [[Submap-Level-Provenance]]
- [[Geometry-vs-Visual-Consistency]]
