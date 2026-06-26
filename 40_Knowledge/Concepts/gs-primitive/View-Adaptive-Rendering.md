# View-Adaptive Rendering

## Definition

Gaussian 的 opacity、color、scale、rotation 等属性不固定，而是根据当前观察视角的方向和距离动态预测。在 Scaffold-GS 中由 small MLPs 根据 integrated anchor feature、relative viewing direction、relative distance 解码。

## Why it matters

Vanilla 3DGS 的 view-dependent effects 被"烘焙"进固定的 SH color 和大量 Gaussian 中，对大视角变化、多尺度观察、反光/阴影不稳。View-adaptive rendering 让同一 anchor 在不同观察条件下产生不同的渲染响应，无需为每个视角存储额外 Gaussian。

## Key distinctions

| | Fixed attributes (3DGS) | View-adaptive (Scaffold-GS) |
|---|---|---|
| Opacity | 固定 | 随视角/距离变化 |
| Color | SH coefficients | MLP 动态解码 |
| Scale | 固定 | 可视角相关 |
| Rotation | 固定 | 可视角相关 |
| Multi-scale | 弱 | 强 |

## 安全边界

- **Appearance view-adaptive**: 合理（反射、光照）
- **Geometry view-adaptive**: 危险（可能掩盖真实几何错误）
- 建议分工：geometry slow-changing multi-view consistent，appearance allowed view-adaptive

## Related notes

- [[matching-representation/Scaffold-GS]]
- [[Anchor-Scaffold-Representation]]
