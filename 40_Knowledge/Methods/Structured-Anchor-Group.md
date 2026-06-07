# Structured Anchor Group

## Goal

将 SLAM anchors 从**孤立的独立点**升级为具有语义和几何 primitive 支撑的**结构化 anchor group**，使 anchor 不再是单点独立成熟，而是通过 group support 获得更稳定的结构约束。

## Background

S3LAM 的核心启发：单个 point 不可靠，但一组属于同一 semantic instance 的 points 可以支持一个 structure。对应到 anchor 方向：single anchor evidence weak，anchor cluster 有 shared support，可以拟合 primitive，判断结构稳定性。

## Design

### Variables
- **anchor a_i**: 3D position, normal, confidence, support observations, temporal residual history, semantic label distribution, group id
- **cluster C_k**: semantic class, instance id, anchor members, fitted primitive, inlier ratio, maturity score
- **primitive π_k**: plane / tangent patch / local surface, normal, offset, uncertainty

### Factors
- reprojection / tracking residual
- depth consistency residual
- normal consistency residual
- anchor-to-primitive residual
- semantic support residual
- temporal survival score
- dynamic rejection score

### Admission pipeline (双门控)
语义/结构 candidate → 依次通过：
1. segmentation confidence pass
2. temporal visibility pass
3. depth consistency pass
4. normal consistency pass
5. primitive fit pass
6. residual / inlier pass
→ **only then**: structural prior admitted

## Contrast with S3LAM

| | S3LAM | Structured Anchor Group |
|---|---|---|
| Primitive | plane only | plane, line, quadric, cuboid, local tangent patch |
| Constraint type | point-plane regularizer | anchor-to-primitive, normal-to-primitive, scale prior |
| Optimization | g2o BA + unary edge | GS rendering + temporal + structural |
| Dynamic handling | 无 | temporal survival + dynamic rejection score |

## Extensible primitive types

- plane: wall / floor / table / road
- line / edge: furniture boundary / corner
- quadric / ellipsoid: chair / object extent
- cuboid: box-like object / room layout
- local tangent patch: generic surface primitive (最匹配 GS)

## 对 GS 的适配

GS 优化对象不同（position/scale/rotation/opacity/SH），约束需改写为：
- anchor-to-plane residual
- Gaussian center-to-plane residual
- Gaussian normal-to-plane residual
- Gaussian scale anisotropy prior
- semantic consistency residual
- visibility-consistent support weight

## Related notes

- [[S3LAM]]
- [[Semantic-Cluster-Structure-Prior]]
- [[Point-Plane-Regularizer]]
