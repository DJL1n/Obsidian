# SNR-Aware Reliability Gating

## Definition

在几何估计中，不假设输入（RGB/event/depth）在所有区域同等可靠，而是估计局部观测可靠性（SNR/texture gradient/flow consistency/depth-normal stability），再决定该区域是否可参与 geometry certification / anchor maturity。EAG3R 的 SNR-aware fusion 是代表实现。

## Why it matters

RGB 在低光/过曝/模糊区域不可靠。如果不做 reliability gating，anchor 可能在不可靠几何上 birth，污染 persistent map。对应你的系统：candidate anchor 应有 local observation reliability score → 高可靠进 maturity / GS birth；低可靠只作 transient。

## Related notes

- [[EAG3R]]
- [[Motion-Geometry-Coherence-Loss]]
- [[Event-Augmented-Geometry-Candidate]]
