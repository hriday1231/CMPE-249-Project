# Operating Points in 3D Semantic Occupancy: A Pre-Registered Measurement Study

**Course:** CMPE 249, Intelligent Autonomous Systems, Fall 2026, Prof. Kaikai Liu  
**Team:** Hriday Ampavatina, Aditya Hegde, Pramod Yadav  
**Selected track:** Research, with a System deliverable  

## Abstract

3D semantic occupancy models are ranked on the Occ3D-nuScenes benchmark by mIoU and, since
2024, by RayIoU. Both metrics are computed after a decision step that converts continuous
per-voxel model scores into a discrete occupied set. For models that expose an occupancy
score, that decision step contains a free scalar, an operating point, which is selected at
inference time, is not fixed by the benchmark protocol, and is not reported in published
results.

Two questions follow, and neither has been answered on held-out data. First, how far does
that operating point move the reported metrics relative to the published differences
between methods? Second, do the metrics agree with each other, and with a safety-side
quantity, about where the operating point should sit? The second question matters because
both metrics weight all voxels equally: a voxel 40 metres behind the vehicle contributes as
much as one 5 metres ahead in the travel lane.

This project measures, on pre-specified score-compatible frozen checkpoints and on
scene-disjoint held-out data, how far post-hoc probability calibration and output-geometry
perturbation move mIoU, RayIoU, and a pre-registered ego-corridor false-negative proxy, and
whether those three quantities select different operating points. We pre-register the
expectation that RayIoU resists these changes more than voxel mIoU, and report that
contrast as the primary result whether or not it holds.

This is a measurement study. It is not a general calibration method and it does not make a
safety guarantee. The corridor metric is an offline risk-weighted error proxy. The
engineering deliverable is a reproducible evaluator that runs on cached model outputs
without a GPU.

## Repository

https://github.com/hriday1231/CMPE-249-Project

## Deliverables

| Deliverable | File |
|---|---|
| A. Repository and README | this file |
| B. Literature and SOTA survey | `Literature review.md` |
| C. Project proposal | `CMPE249_Project_Proposal.docx`, also submitted to Canvas |
| D. AI novelty and feasibility audit | `AI novelty & Feasibility Audit.md` |
