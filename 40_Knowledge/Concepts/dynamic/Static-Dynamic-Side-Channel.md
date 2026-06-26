# Static-Dynamic Side Channel

## Definition

在 dynamic GS-SLAM 中，将 uncertainty/dynamic risk 作为侧通道信息，用于降权/隔离动态区域，但不作为主几何认证信号。WildGS-SLAM 的 uncertainty 是 reliability weight，不是严格的 static/dynamic label。

## Why it matters

Uncertainty 不等于 geometry certificate。高 uncertainty 可能是动态物体，也可能是复杂静态纹理。低 uncertainty 可能是静态表面，也可能是长期静止的动态物体。因此 uncertainty 只能作为 side channel 影响权重，不能替代 geometry certification。

## Related notes

- [[dynamic-gs/WildGS-SLAM]]
- [[Uncertainty-Dynamic-Filtering]]
- [[Certified-Dynamic-Gate]]
