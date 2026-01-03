layout: page
title: Efficient Vision-Language-Action Driving Models
description: Token Merging for SimLingo & ReCogDrive
img: assets/img/12.jpg
importance: 1
category: work
related_publications: true
---
**Efficient Vision-Language-Action Models for Autonomous Driving**

Location: Ann Arbor, MI

Built and evaluated training-free Token Merging (ToMe) inside state-of-the-art VLA stacks (SimLingo, ReCogDrive) to shrink inference cost while keeping driving quality stable in closed-loop tests.

**What I did**
- Integrated ToMe into VLA backbones end-to-end; achieved up to 60% GFLOPs reduction with matching latency and memory savings.
- Ran synchronized 4-GPU closed-loop evaluations on Bench2Drive and NAVSIM; measured an average ~10% relative PDMS drop to map the efficiency–safety tradeoff.
- Instrumented per-stage telemetry (vision, language, action heads) to trace degradation sources and tune merge ratios without retraining.

**Results at a glance**
- Up to 60% lower inference GFLOPs and VRAM use across SimLingo and ReCogDrive.
- ~10% relative PDMS drop in closed-loop driving; acceptable for cost-sensitive deployments.
- Reproducible multi-GPU harness with synchronized seeds and logging for fair comparisons.

**System snapshot**
<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/1.jpg" title="Token merging inside VLA encoder" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/3.jpg" title="Bench2Drive closed-loop runs" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/5.jpg" title="Latency vs safety tradeoff" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Left: ToMe reduces token count before language grounding. Middle: Bench2Drive rollouts with synchronized seeds. Right: Efficiency–safety frontier from merge-ratio sweeps.
</div>

**Tech**
- Frameworks: PyTorch Lightning, CUDA graphs
- Tooling: Bench2Drive, NAVSIM, 4xGPU synchronized evaluation
- Optimizations: Token Merging, dynamic batching, mixed precision

**Next steps**
- Add per-scenario PDMS breakdown (merging ratio vs. failure modes).
- Visualize token pruning heatmaps over driving scenes.
- Compare ToMe with lightweight distillation to benchmark accuracy vs cost.
