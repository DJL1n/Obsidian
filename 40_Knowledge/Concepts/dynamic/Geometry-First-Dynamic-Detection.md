# Geometry-First Dynamic Detection

## Definition

用几何一致性判断动态区域，而非语义分割：真实 optical flow ≈ camera-motion-induced flow (from pointmap + pose) → static；不一致 → dynamic / unreliable。MonST3R 的 confident static region 是代表实现。

## Why it matters

语义分割依赖预定义类别，泛化有限。Geometry-first 将动态视为 "无法被纯净相机运动解释的观测"，直接适配 anchor admission：若某区域的 DPVO flow ≠ depth-induced flow，则该区域不应进入 mature anchor / persistent GS。

## Related notes

- [[dynamic-gs/MonST3R]]
- [[Per-Timestep-Pointmap]]
- [[Dynamic-Region-as-Packet-Evidence]]
