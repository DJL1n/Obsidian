# AfME Appearance Embedding

## Goal

在 GS-SLAM 中从 camera pose / motion 提取 latent appearance embedding，建模 per-image appearance variations（曝光、视角、光照、运动），防止 appearance mismatch 错误驱动 geometry update。

## Design

- Input: camera pose / motion-related signal
- Output: latent appearance embedding
- Usage: condition Gaussian rendering / color prediction per view
- Not: per-image free latent code — embedding tied to motion/pose context

## Related notes

- [[SEGS-SLAM]]
- [[Appearance-Geometry-Decoupling]]
- [[Structured-GS-for-SLAM]]
