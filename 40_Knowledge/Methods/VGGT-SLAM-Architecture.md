# VGGT-SLAM Architecture

## Goal

Dense RGB SLAM: VGGT feed-forward submaps + SL(4) manifold factor graph global alignment。

## Components

- Keyframe selection: Lucas-Kanade disparity threshold
- Submap: VGGT on local window (shared overlap frame, loop retrieved frames)
- Local geometry: VGGT camera head → intrinsics/extrinsics → depth → inverse projection → dense point cloud
- Relative alignment: 5-pt RANSAC → SL(4) homography between overlapping submaps
- Loop: SALAD retrieval → include loop frames in current submap → relative SL(4) homography
- Backend: SL(4) factor graph → LM on Lie algebra → globally aligned submaps
- Output: globally consistent dense RGB point cloud

## Related notes

- [[VGGT-SLAM]]
- [[Projective-Ambiguity-Awareness]]
- [[Feed-Forward-Submap]]
