---
layout: page
title: Efficient VLA Models for Self-Driving
description: Token Merging techniques for reducing computational costs in Vision-Language-Action models for self-driving cars while maintaining safety performance and inference efficiency
img: assets/img/simlingo.jpg
importance: 2
category: work
giscus_comments: false
---

## Motivation

Current Vision-Language-Action models for autonomous driving rely on large vision encoders that impose heavy computational and memory costs, often exceeding 100 GFLOPs per inference. These requirements limit deployment on vehicle-mounted edge hardware where compute budgets remain below 10 TOPS and response times must be under 100 ms. Attention mechanisms scale quadratically, creating further inefficiencies for high-resolution video inputs.

Token Merging (ToMe) provides a solution by identifying and merging redundant visual tokens throughout transformer layers, enabling up to three times faster inference and substantially lower memory usage without retraining. By grouping semantically similar patches into fewer and more informative tokens, ToMe preserves essential representational structure while functioning as a lightweight, easily integrated plug-in for Vision Transformers.

### Methodology

**Frameworks Evaluated**
- **SimLingo**: An end-to-end VLA model using InternViT-6B vision encoder and Qwen2 LLM, excelling in closed-loop CARLA evaluation
- **ReCogDrive**: A hierarchical VLA system emphasizing scene-level reasoning with a diffusion-based trajectory planner, achieving superior collision avoidance in complex scenarios

**Token Merging Integration**
- Integrated ToMe directly into the vision encoder of both frameworks
- ToMe performs bipartite soft matching to identify and merge semantically redundant visual tokens layer by layer
- Applied proportional attention modification to preserve attention mass and prevent feature attenuation
- Inserted ToMe after self-attention in selected InternViT encoder layers (beginning at layer 12 where redundancy is highest)
- Tested merge budgets: r ∈ {4, 8, 16, 25}
- No weights updated, no fine-tuning required—fully plug-and-play integration

### Key Contributions

**Efficiency Gains**
- Achieved up to 60% reduction in inference GFLOPs with significant latency and memory savings
- Demonstrated 2-3x faster inference and substantially reduced memory usage without retraining

**Comprehensive Evaluation**
- Evaluated SimLingo on Bench2Drive (CARLA simulator with 220 interactive routes)
- Evaluated ReCogDrive on NAVSIM closed-loop environment using Predictive Model Driver Score (PDMS)
- Used 4 GPUs with synchronized parallel evaluation
- Quantified the efficiency–safety tradeoff in VLA driving models
- Demonstrated an average ~10% relative PDMS drop across tested merge budgets

**Safety-Efficiency Trade-off Analysis**
- Found that uniform token merging removes essential fine-grained spatial and temporal cues needed for safe planning
- Performance loss (though moderate in absolute value) exceeds strict safety margins required for production-level autonomy
- Results motivated development of driving-specific merging strategies (depth-aware, object-centric, risk-adaptive methods)

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/3.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/3.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/5.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Caption photos easily. On the left, a road goes through a tunnel. Middle, leaves artistically fall in a hipster photoshoot. Right, in another hipster photoshoot, a lumberjack grasps a handful of pine needles.
</div>
<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/5.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    This image can also have a caption. It's like magic.
</div>

You can also put regular text between your rows of images.
Say you wanted to write a little bit about your project before you posted the rest of the images.
You describe how you toiled, sweated, _bled_ for your project, and then... you reveal its glory in the next row of images.

<div class="row justify-content-sm-center">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/6.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-4 mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/11.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    You can also have artistically styled 2/3 + 1/3 images, like these.
</div>

The code is simple.
Just wrap your images with `<div class="col-sm">` and place them inside `<div class="row">` (read more about the <a href="https://getbootstrap.com/docs/4.4/layout/grid/">Bootstrap Grid</a> system).
To make images responsive, add `img-fluid` class to each; for rounded corners and shadows use `rounded` and `z-depth-1` classes.
Here's the code for the last row of images above:


{% raw %}

```html
<div class="row justify-content-sm-center">
  <div class="col-sm-8 mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/6.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
  </div>
  <div class="col-sm-4 mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/11.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
```

{% endraw %}
