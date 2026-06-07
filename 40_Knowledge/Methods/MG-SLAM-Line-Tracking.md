# MG-SLAM Line Tracking

## Goal

在 RGB-D GS-SLAM 中使用点特征 + 融合线段特征做 camera tracking 和 BA，提升 textureless indoor 场景中的 pose 稳定性。

## Procedure

1. Point features: RGB-D backprojection → 3D → reprojection error
2. Line segments: EDLines + LBD matching + RGB-D backprojection to 3D line
   - Line reprojection error (3D line → image)
   - Line backprojection error (3D point to 3D line distance)
3. Line fusion: merge fragments with similar direction/proximity
4. Full BA: point + line residuals + Huber + Levenberg-Marquardt

## Related notes

- [[MG-SLAM]]
- [[Manhattan-Structure-Prior]]
- [[Observed-vs-Hypothesized-Geometry]]
