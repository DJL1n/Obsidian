# Ray Residual

## Definition

在 SLAM pose optimization 中使用 ray direction error 替代完整的 3D point error 作为残差：

```
ray(p) = normalize(X(p))
residual = angular_error(ray_ref, normalize(T * X_cur))
```

## Why it matters

MASt3R pointmap 的 depth 预测经常不一致。直接最小化 3D point error 会把 pose 拉坏，因为 depth error 会扭曲对齐。Ray residual 对深度误差更不敏感，且 angular error 有界，对 outlier 更稳。

## Key distinctions

| | 3D point residual | Ray residual |
|---|---|---|
| 误差量 | ‖X_ref - T X_cur‖ | angular error between rays |
| 对 depth error 敏感度 | 高 | 低 |
| Error bound | 无界 | 有界 (≤ π) |
| 对 outlier 鲁棒性 | 低 | 高 |
| 退化风险 | 较少 | pure rotation 时需加 distance term |

## Common failure modes

- 纯旋转情况下退化 → 需要加小权重 distance consistency term
- 极端 depth error 时仍可能受影响，但比 3D point residual 好很多

## Application to GeometryPacket

可将 packet consistency 分成三层：

- **Strong term**: angular / ray / bearing consistency（主约束）
- **Weak term**: depth / distance / scale consistency（弱约束）
- **Late term**: GS rendering utility（最终验证）

不要一开始就让 3D depth residual 主导 pose 或 anchor admission。

## Related notes

- [[slam-frontends/large-scale/MASt3R-SLAM]]
- [[Ray-Camera-Model]]
- [[Canonical-Pointmap-Fusion]]
