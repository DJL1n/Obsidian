# 💡 Concepts — 概念笔记

一条笔记一个概念，按主题分类存放。

## geometry/ — 几何基础
相机模型、点图、投影、深度
- [[geometry/Canonical-Pointmap-Fusion|Canonical Pointmap Fusion]]
- [[geometry/Ray-Camera-Model|Ray Camera Model]]
- [[geometry/Ray-Residual|Ray Residual]]
- [[geometry/Pointmap-Regression|Pointmap Regression]]
- [[geometry/Uncalibrated-Dense-Reconstruction|Uncalibrated Dense Reconstruction]]
- [[geometry/Constrained-Pointmap|Constrained Pointmap]]
- [[geometry/Anchor-Depth-Parameterization|Anchor Depth Parameterization]]
- [[geometry/Multi-View-Geometry-Packet|Multi-View Geometry Packet]]
- [[geometry/Projective-Ambiguity-Awareness|Projective Ambiguity Awareness]]
- [[geometry/Two-View-Pointmap-Prior|Two-View Pointmap Prior]]
- [[geometry/Feed-Forward-vs-Optimization|Feed-Forward vs Optimization]]
- [[geometry/Feed-Forward-Submap|Feed-Forward Submap]]
- [[geometry/Persistent-State-Pointmap|Persistent State Pointmap]]
- [[geometry/3D-Grounded-Matching|3D Grounded Matching]]
- [[geometry/Sparse-Learned-Matcher|Sparse Learned Matcher]]

## tracking/ — 追踪
BA、VO、光流、位姿估计
- [[tracking/Dense-Bundle-Adjustment|Dense Bundle Adjustment]]
- [[tracking/Recurrent-Correction-Update|Recurrent Correction Update]]
- [[tracking/Correlation-Volume|Correlation Volume]]
- [[tracking/Patch-Graph-VO|Patch Graph VO]]
- [[tracking/Patch-Lifecycle|Patch Lifecycle]]
- [[tracking/Explicit-Tracking-vs-Render-Tracking|Explicit Tracking vs Render Tracking]]
- [[tracking/EM-VO-GS-Coupling|EM VO-GS Coupling]]
- [[tracking/Frontend-Gaussian-Initialization|Frontend Gaussian Initialization]]
- [[tracking/MCBA-Cross-View-Constraint|MCBA Cross-View Constraint]]
- [[tracking/Motion-Geometry-Coherence-Loss|Motion-Geometry Coherence Loss]]
- [[tracking/Explicit-Birth-Event|Explicit Birth Event]]

## gs-primitive/ — GS 表示
3DGS primitive、渲染、表示
- [[gs-primitive/GS-Tracking-Representation|GS Tracking Representation]]
- [[gs-primitive/Gaussian-Covisibility|Gaussian Covisibility]]
- [[gs-primitive/Structured-GS-for-SLAM|Structured GS for SLAM]]
- [[gs-primitive/Appearance-Geometry-Decoupling|Appearance-Geometry Decoupling]]
- [[gs-primitive/Multi-Level-Pyramid-GS|Multi-Level Pyramid GS]]
- [[gs-primitive/Multi-Channel-Gaussian-Attributes|Multi-Channel Gaussian Attributes]]
- [[gs-primitive/Predict-and-Refine-Gaussian|Predict-and-Refine Gaussian]]
- [[gs-primitive/Deformable-GS-Map|Deformable GS Map]]
- [[gs-primitive/Submap-Gaussian-Organization|Submap Gaussian Organization]]
- [[gs-primitive/GS-Birth-Gate|GS Birth Gate]]
- [[gs-primitive/View-Adaptive-Rendering|View-Adaptive Rendering]]
- [[gs-primitive/Anchor-Scaffold-Representation|Anchor Scaffold Representation]]
- [[gs-primitive/Hybrid-TSDF-GS-Map|Hybrid TSDF-GS Map]]
- [[gs-primitive/Adaptive-Gaussian-Expansion|Adaptive Gaussian Expansion]]
- [[gs-primitive/Coarse-to-Fine-GS-Tracking|Coarse-to-Fine GS Tracking]]

## anchor/ — Anchor 骨架
生命周期、birth、maturity、provenance
- [[anchor/Octree-Anchor-Hierarchy|Octree Anchor Hierarchy]]
- [[anchor/Progressive-LOD-Growth|Progressive LOD Growth]]
- [[anchor/Anchor-Level-Context-Model|Anchor-Level Context Model]]
- [[anchor/Hierarchical-Anchor-Partition|Hierarchical Anchor Partition]]
- [[anchor/Probabilistic-Anchor-Update|Probabilistic Anchor Update]]
- [[anchor/Primitive-Birth-Provenance|Primitive Birth Provenance]]
- [[anchor/Primitive-Lifecycle-State|Primitive Lifecycle State]]
- [[anchor/Alignment-Check-Before-Birth|Alignment Check Before Birth]]
- [[anchor/Multi-Scale-Anchor-Admission|Multi-Scale Anchor Admission]]
- [[anchor/Submap-Level-Provenance|Submap-Level Provenance]]
- [[anchor/Geometry-Gated-GS-Birth|Geometry-Gated GS Birth]]

## dynamic/ — 动态场景
动态物体、不确定性、mask 融合
- [[dynamic/Uncertainty-Dynamic-Filtering|Uncertainty Dynamic Filtering]]
- [[dynamic/Static-Dynamic-Side-Channel|Static-Dynamic Side Channel]]
- [[dynamic/Consistency-Dynamic-Detection|Consistency Dynamic Detection]]
- [[dynamic/Occlusion-vs-Exposure-Cue|Occlusion vs Exposure Cue]]
- [[dynamic/Multi-Evidence-Dynamic-Fusion|Multi-Evidence Dynamic Fusion]]
- [[dynamic/Dynamic-Suppression-Both-Sides|Dynamic Suppression Both Sides]]
- [[dynamic/Geometry-First-Dynamic-Detection|Geometry-First Dynamic Detection]]
- [[dynamic/Per-Timestep-Pointmap|Per-Timestep Pointmap]]
- [[dynamic/Explicit-Implicit-Dynamic-Fusion|Explicit-Implicit Dynamic Fusion]]
- [[dynamic/Dynamic-Gate-Through-Pipeline|Dynamic Gate Through Pipeline]]
- [[dynamic/Multi-Modal-Uncertainty|Multi-Modal Uncertainty]]
- [[dynamic/Primitive-Level-Reliability|Primitive-Level Reliability]]
- [[dynamic/Appearance-vs-Geometry-Uncertainty|Appearance vs Geometry Uncertainty]]

## submap/ — 子图
子图、变形、融合、loop
- [[submap/Submap-Geometry-Unit|Submap Geometry Unit]]
- [[submap/2D-3D-Fusion-Tracking|2D-3D Fusion Tracking]]
- [[submap/Spatial-Memory-Reconstruction|Spatial Memory Reconstruction]]
- [[submap/Dense-Sparse-Memory|Dense-Sparse Memory]]
- [[submap/Online-Global-Pose-Graph|Online Global Pose Graph]]
- [[submap/Versioned-Geometry-Update|Versioned Geometry Update]]
- [[submap/Silhouette-Map-Coverage|Silhouette Map Coverage]]
- [[submap/Isotropic-GS-SLAM|Isotropic GS-SLAM]]
- [[submap/Certified-Geometry-Memory|Certified Geometry Memory]]
- [[submap/Gaussian-Map-Deformation|Gaussian Map Deformation]]
- [[submap/Local-Map-Tracking|Local Map Tracking]]
- [[submap/Geometry-vs-Visual-Consistency|Geometry vs Visual Consistency]]
- [[submap/Hidden-State-Submap-Descriptor|Hidden-State Submap Descriptor]]
- [[submap/Monocular-GS-SLAM-Pipeline|Monocular GS-SLAM Pipeline]]

## semantic/ — 语义
语义特征、CLIP/DINO、scene understanding
- [[semantic/Semantic-Cluster-Structure-Prior|Semantic Cluster Structure Prior]]
- [[semantic/Point-Plane-Regularizer|Point-Plane Regularizer]]
- [[semantic/Tri-Plane-Feature|Tri-Plane Feature]]
- [[semantic/TSDF-Direct-Supervision|TSDF Direct Supervision]]
- [[semantic/Adaptive-Computation-Graph|Adaptive Computation Graph]]
- [[semantic/SDF-Geometry-Teacher|SDF Geometry Teacher]]
- [[semantic/Shape-Regularization|Shape Regularization]]
- [[semantic/Manhattan-Structure-Prior|Manhattan Structure Prior]]
- [[semantic/Structure-Guided-GS-Completion|Structure-Guided GS Completion]]
- [[semantic/I2P-Local-Reconstruction|I2P Local Reconstruction]]
- [[semantic/L2W-Global-Registration|L2W Global Registration]]
- [[semantic/Residual-GS-Layer|Residual GS Layer]]
- [[semantic/Geometry-First-GS-Pipeline|Geometry-First GS Pipeline]]
- [[semantic/Compact-Semantic-Memory|Compact Semantic Memory]]
- [[semantic/Multi-Representation-Geometry-Admission|Multi-Representation Geometry Admission]]

## scale/ — 尺度校准
尺度校准、几何认证、coherence
- [[scale/Sparse-Dense-Scale-Alignment|Sparse-Dense Scale Alignment]]
- [[scale/JDSA-Scale-Alignment|JDSA Scale Alignment]]
- [[scale/Pose-Depth-Co-Versioning|Pose-Depth Co-Versioning]]
- [[scale/Geometry-Authority-Separation|Geometry Authority Separation]]
- [[scale/Surface-Aware-Depth-Rendering|Surface-Aware Depth Rendering]]
- [[scale/SNR-Aware-Reliability-Gating|SNR-Aware Reliability Gating]]

---

> 概念笔记是知识的原子单元。
