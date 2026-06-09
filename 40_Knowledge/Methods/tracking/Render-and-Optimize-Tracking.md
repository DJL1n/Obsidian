# Render-and-Optimize Tracking

## Goal

在 GS-SLAM 中通过渲染当前 map 并与输入 RGB-D frame 对齐来估计相机位姿，使用 alpha mask 和 error mask 控制哪些像素参与优化。

## Background

Gaussian-SLAM 的 tracking 是 frame-to-model：渲染 active sub-map 得到 color/depth/alpha，与输入 RGB-D 做 loss，Adam 优化 pose。使用 soft alpha mask + error/inlier mask 避免未建图/错误区域污染 pose。

## Procedure

1. **Pose initialization**: constant speed assumption
2. **Render**: active sub-map → rendered color, depth, alpha
3. **Alpha mask**: alpha high → map 已覆盖，loss 权重大；alpha low → 新/未重建区域，权重低
4. **Error/inlier mask**: color/depth error > frame-relative threshold → discard pixel
5. **Optimize**: minimize color error + depth error, freeze Gaussian params

## Key design points

- 冻结 Gaussian（只优化 pose）
- Soft alpha mask（polynomial mask from rendered alpha）
- Error boolean mask（剔除大残差 pixels）
- 只有在 map 本身可信区域的 render loss 才约束 pose

## Related notes

- [[Gaussian-SLAM]]
- [[GS-Birth-Gate]]
