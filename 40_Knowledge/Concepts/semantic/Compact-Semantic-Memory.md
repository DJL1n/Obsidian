# Compact Semantic Memory

## Definition

在 semantic GS-SLAM 中，不直接在 Gaussian/anchor 上存高维 CLIP/DINO feature，而是 primitive 存 low-dimensional semantic feature/descriptor，global memory bank 存 high-dimensional prototypes，通过 attention/retrieval 读取。OpenMonoGS-SLAM 的 memory bank 是代表实现。

## Why it matters

高维语言/视觉特征直接挂在每个 primitive 上会导致爆炸性显存和存储成本。Low-dim feature + high-dim memory 结构更可控、可扩展、可在线更新。对应你的 anchor：compact semantic/risk descriptor + memory bank of object/region/dynamic prototypes。

## Related notes

- [[OpenMonoGS-SLAM]]
- [[Multi-Scale-Anchor-Admission]]
- [[Semantic-Backend-Regularizer]]
