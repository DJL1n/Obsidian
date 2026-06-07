# Spann3R Memory Architecture

## Goal

通过 external spatial memory 让 DUSt3R-style pointmap reconstruction 扩展到增量多帧，无需 test-time global alignment。

## Components

### Memory encoder
Takes decoded feature + predicted pointmap → memory key/value features (visual + geometry)。

### Memory query
Previous frame's query feature attends to memory keys/values via cross-attention → fused memory feature。

### Working memory
- 最近 5 帧 dense memory
- 新插入前 similarity check（最大相似度低于阈值才插入）
- 满了后最老的 drain 到 long-term

### Long-term memory
- Sparse
- 累计 attention weight
- 超过阈值后只保留 top-k tokens（~4000）
- 防止显存无限增长

### Attention clipping
- 小 attention weight tokens 可能含 outlier
- Hard clipping + renormalize → 防止 outlier 污染 fused feature

## Inference flow
```
current image → ViT → visual feature V_t
previous query q_{t-1} → cross-attention over memory → fused feature M_t
V_t + M_t → decoders → global pointmap X_t
→ update memory → new query q_t
```

## Related notes

- [[Spann3R]]
- [[Spatial-Memory-Reconstruction]]
- [[Dense-Sparse-Memory]]
- [[Spatial-Memory-for-Anchor]]
