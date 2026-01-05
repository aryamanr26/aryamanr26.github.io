---
layout: page
title: SuPerMVO: Learning-Augmented Monocular Visual Odometry
description: A hybrid monocular visual odometry pipeline combining SuperPoint, SuperGlue, and GTSAM to reduce drift and improve robustness in low-texture driving environments
img: assets/img/superglue.png
importance: 2
category: work
giscus_comments: false
---

## Motivation

Monocular visual odometry is attractive due to its low cost and wide availability, but classical pipelines such as ORB-SLAM struggle in texture-sparse and dynamic environments. These failures lead to unstable tracking, poor feature correspondences, and severe scale drift, particularly in forward-motion driving scenarios.

Recent advances in deep learning-based feature extraction provide a robust alternative. Learned keypoints leverage global image context and semantic cues, enabling reliable correspondence even where gradient-based methods fail. This project explores how such learned representations can be integrated with classical geometric optimization to improve monocular visual odometry.

## Approach

SuPerMVO is a purely monocular visual odometry pipeline that fuses deep feature matching with graph-based pose optimization.

### Pipeline Overview

- **Feature Detection**  
  SuperPoint jointly detects interest points and computes descriptors in a single forward pass, enabling robust keypoints even in low-texture regions.

- **Feature Matching**  
  SuperGlue performs context-aware matching using transformer-based attention, producing high-confidence correspondences under viewpoint changes and repetitive patterns.

- **Relative Pose Estimation**  
  Matched features are used to estimate the Essential matrix via RANSAC, recovering full 6-DoF relative pose between frames.

- **Keyframe Selection**  
  Frames are promoted to keyframes when translational displacement exceeds 0.01 m, reducing redundancy and drift accumulation.

- **Pose Graph Optimization**  
  A factor graph is constructed in GTSAM using odometry constraints and priors, and optimized via Levenberg–Marquardt.

- **Scale Alignment**  
  Final trajectories are aligned to ground truth using evo's Umeyama-based similarity alignment, enabling metric evaluation despite monocular scale ambiguity.

## Dataset and Evaluation

The system is evaluated on the KITTI odometry benchmark using only monocular RGB images. Camera intrinsics are fixed across sequences, and ground-truth poses are parsed from KITTI pose files.

Evaluation metrics include:
- Absolute Pose Mean Error (APME)
- Absolute RMSE
- Relative Pose Mean Error (RPME)
- Relative RMSE

Both raw and scale-aligned trajectories are evaluated to analyze drift behavior.

## Results

### Qualitative Results

SuPerMVO produces trajectories that closely follow KITTI ground truth across multiple sequences. Compared to ORB-based pipelines, SuPerMVO:
- Maintains global trajectory shape
- Exhibits significantly lower drift
- Performs robustly in low-texture and dynamic scenes

Orientation estimates show strong pitch and yaw tracking, with expected limitations in roll due to monocular constraints.

### Quantitative Results

Across KITTI sequences 00, 06, 09, and 10:
- SuPerMVO consistently outperforms ORB-based baselines
- Scale-aligned SuPerMVO achieves the lowest errors in both global and local metrics
- Performance gains are most pronounced in texture-poor environments

These results demonstrate that learned feature representations, when combined with classical optimization, significantly improve monocular VO robustness.

## Limitations

- **Loop Closure**  
  A Bag-of-Words loop closure module was explored but proved unreliable due to repetitive environments and limited visual distinctiveness.

- **Bundle Adjustment**  
  Sliding-window bundle adjustment underperformed due to sparse correspondences and planar road geometry.

Both components remain supported by the framework but were excluded from final evaluation.

## Key Contributions

- Hybrid deep-learning and geometric monocular VO pipeline  
- Robust feature matching in low-texture driving scenes  
- Significant reduction in scale drift and trajectory deformation  
- Clean integration of SuperPoint, SuperGlue, GTSAM, and evo  

## Future Work

- Transformer-based semantic loop closure  
- IMU or stereo fusion for absolute scale recovery  
- Real-time embedded deployment  
- Extension to a full SLAM system with global mapping  

## Resources

- **Code**: https://github.com/aryamanr26/NA568-Project-Group22  
- **Video Demo**: https://www.youtube.com/watch?v=dF_nQ6IA1po
