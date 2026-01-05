---
layout: page
title: Motion Planning Performance Analysis
description: Empirical evaluation of sampling-based motion planners including RRT-connect, RRT*, Informed RRT*, BiRRT*, and PRM* in obstacle-dense environments on a 6-DOF robotic arm
img: assets/img/3.jpg
importance: 2
category: work
giscus_comments: false
---

## Abstract

Sampling-based motion planners are widely used for high-dimensional robotic manipulation in cluttered environments. This work evaluates several common planners, including RRT, RRT-Connect, RRT*, BiRRT*, Informed RRT*, Lazy PRM, and PRM*, on a 6-DOF PR2 arm in a simulated tabletop setting with obstacles. Each planner is tested over multiple trials using fixed start and goal configurations to account for stochasticity. Performance is compared using metrics covering computational efficiency and trajectory quality, including planning time, node expansions, path length, waypoint count, end-effector travel distance, and joint-space discontinuity. Shortcut-based smoothing is applied to all successful plans to assess trajectory refinement. The results highlight trade-offs between speed, optimality, and smoothness, providing practical guidance for planner selection in cluttered manipulation tasks.

**Complete source code:** [github.com/aryamanr26/rrt-planning-suite](https://github.com/aryamanr26/rrt-planning-suite)

## Introduction

Motion planning is a fundamental problem in robotics, particularly for robotic manipulators operating in constrained and cluttered environments. A robot must compute a collision-free trajectory that moves its joints from a start configuration to a desired goal while respecting kinematic limits and avoiding obstacles. This problem becomes increasingly challenging as the dimensionality of the system grows and as the environment becomes more complex, making classical grid-based or deterministic planning methods computationally infeasible.

Sampling-based motion planning algorithms have emerged as a practical solution to these challenges. Methods such as Rapidly-exploring Random Trees (RRT) [1] and Probabilistic Roadmaps (PRM) [2] avoid explicit construction of the full configuration space and instead rely on random sampling to incrementally explore feasible regions. These algorithms are particularly well-suited for high-degree-of-freedom robotic systems, where exact representations of the configuration space are prohibitively expensive. As a result, sampling-based planners have been widely adopted in real-world robotic systems for manipulation, navigation, and autonomous operation.

Robotic manipulation in tabletop environments is a representative and practically important application domain for motion planning. Tasks such as pick-and-place, object rearrangement, and tool use require a robot to plan precise arm motions while avoiding collisions with tables, objects, and the robot's own body. These scenarios are common in industrial automation, service robotics, and assistive robotics, where safety and reliability are critical. Planning in such environments requires not only finding feasible paths, but also producing trajectories that are efficient, smooth, and suitable for execution on real hardware.

This work systematically compares RRT, RRT-Connect, RRT*, BiRRT*, Informed RRT*, Lazy PRM, and PRM* using a simulated PR2 robot in a cluttered tabletop environment (Fig. 1). Through repeated trials with fixed start and goal configurations, we evaluate trade-offs between planning speed, path optimality, and smoothness to guide planner selection for manipulation tasks.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/1.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/3.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/5.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Fig. 1. Visualization of the PR2 robot environment. The PR2's 6-DOF arm operates in a cluttered tabletop setting containing obstacles. The red line shows the raw trajectory produced by the motion planning algorithm, while the blue line represents the corresponding smoothed trajectory after post-processing.
</div>

## Implementation

### Sampling-Based Motion Planning Overview

All planners compute collision-free trajectories for the PR2's 6-DOF left arm using random sampling and collision-checked connections. They differ in sampling strategy, connection methods, and path optimization approach.

### Rapidly-Exploring Random Tree (RRT)

RRT incrementally builds a tree from the start configuration by sampling random configurations and extending toward them. It rapidly finds feasible paths but produces suboptimal trajectories without path cost minimization.

#### RRT-Connect

RRT-Connect grows two trees simultaneously from start and goal, with aggressive bidirectional connection attempts. This significantly improves convergence speed while prioritizing feasibility over optimality.

#### RRT*

RRT* adds asymptotic optimality through a rewiring step that reconnects nearby nodes to reduce path cost. Solutions converge to optimal paths with increased samples, at the cost of higher computational overhead.

#### Bidirectional RRT* (BiRRT*)

BiRRT* combines bidirectional search with RRT* optimality. Two RRT* trees grow from start and goal, periodically connecting and rewiring to reduce path cost. Converges faster than RRT* but is parameter-sensitive.

#### Informed RRT*

Informed RRT* restricts sampling to a heuristic-informed subset after finding an initial solution, focusing on regions that can improve the current path. This accelerates convergence to shorter paths while reducing wasted exploration.

### Probabilistic Roadmap Methods

PRM methods build a roadmap graph during preprocessing by sampling collision-free configurations. During query phase, start and goal are connected to the roadmap and a path is extracted via graph search.

#### Lazy PRM

Lazy PRM postpones edge collision checking until query phase, reducing upfront cost but risking edge invalidation during validation.

#### PRM*

PRM* provides asymptotic optimality guarantees with increased samples, at the cost of denser connectivity and more collision checking.

### Performance Metrics

Planners are evaluated using metrics covering computational efficiency and trajectory quality, averaged over multiple runs to account for stochasticity.

#### Planning Time and Node Expansion

Planning time measures computational efficiency, while node expansion quantifies search effort required to find a solution.

#### Raw Path Length

Total joint-space trajectory length before post-processing, computed as the sum of Euclidean distances between consecutive configurations.

#### Raw Waypoint Count

Number of configurations in the initial solution path. High counts indicate irregular paths that affect execution and smoothing.

#### Path Smoothing and Improvement Percentage

To improve trajectory quality, shortcut-based path smoothing is applied to all successful plans. The smoothed path length is computed using the same joint-space distance metric as the raw path length. The improvement percentage quantifies the relative reduction in path length achieved through smoothing and is defined as:

$$\text{Improvement} = \left(1 - \frac{L_{\text{smooth}}}{L_{\text{raw}}}\right) \times 100\%$$

where $L_{\text{raw}}$ and $L_{\text{smooth}}$ denote the raw and smoothed path lengths, respectively.

#### End-Effector Travel Distance

Total Cartesian distance traveled by the end-effector along the smoothed trajectory. Lower values indicate more efficient manipulation.

#### Maximum Joint Jump

Largest change between consecutive joint configurations in the smoothed trajectory. Large jumps indicate reduced smoothness and potential execution difficulty.

Shortcut-based smoothing was applied uniformly to all plans, replacing path segments with shorter collision-free shortcuts to reduce redundancy and improve execution quality.

## Results and Discussion

All planners were evaluated over 20 independent trials on a tabletop task with fixed start and goal configurations. Collision checking, parameters, and smoothing were kept consistent to ensure fair comparison (Fig. 2).

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/5.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    This image can also have a caption. It's like magic.
</div>

**Planning Time:** Informed RRT* required the longest time (200–250 s), PRM* moderate time (30–50 s), while other planners completed in under 30 s, showing a clear speed-quality trade-off.

**Task-Space Performance:** RRT-based planners exhibit higher end-effector travel distances than PRM-based methods. Informed RRT* and RRT-Connect show the largest improvements after smoothing, revealing significant raw path redundancy.

**Path Length:** Lazy PRM and PRM* generate the shortest paths due to global connectivity, while tree-based planners produce longer trajectories from rapid exploration.

**Joint-Space Smoothness:** RRT-Connect achieves the lowest joint discontinuity. RRT and RRT-Connect produce the highest waypoint counts from incremental tree growth.

**Overall:** PRM-based planners consistently produce shorter, smoother solutions. RRT-based planners prioritize rapid exploration and benefit significantly from post-processing.

No single algorithm performs best across all evaluation metrics. The choice of planner therefore depends on which characteristics, such as planning time or waypoint count, are most critical for a given application. However, if a general-purpose planner must be selected, our experiments indicate that **RRT-Connect performs well across more than 50% of the evaluated metrics**. Overall, there is no one-size-fits-all solution in motion planning, and algorithm selection should be guided by the specific requirements of the task and environment.
