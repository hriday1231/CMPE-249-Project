# Literature and SOTA Survey

**Project:** Operating points in 3D semantic occupancy  
**Course:** CMPE 249, Fall 2026  

Twelve works relevant to the project, all with resolved arXiv identifiers.

---

### 1. Fully Sparse 3D Occupancy Prediction (SparseOcc)
`arXiv 2312.17118`, ECCV 2024

Introduces a fully sparse camera-only occupancy network that reconstructs a sparse 3D
representation and predicts semantic and instance occupancy with sparse queries, avoiding dense
volumes and global attention. It also introduces **RayIoU**, a ray-based evaluation metric
designed to solve the inconsistency penalty along depths that traditional voxel-level mIoU
criteria raise. This is the direct antecedent for our work: it establishes that voxel mIoU
rewards predicting a thicker surface, and it proposes RayIoU as the remedy. It does not measure
how far a post-hoc operating point applied to a frozen checkpoint moves either metric on held-out
data, which is the question we take up.

### 2. Occ3D: A Large-Scale 3D Occupancy Prediction Benchmark for Autonomous Driving
`arXiv 2304.14365`, NeurIPS 2023

Defines the benchmark and label set we evaluate on. Contributes a label generation pipeline with
voxel densification, occlusion reasoning and image-guided voxel refinement, producing dense
visibility-aware labels, and establishes the Occ3D-Waymo and Occ3D-nuScenes benchmarks. The
visibility-aware labels and the default camera-mask protocol are what make the operating point
a free and unreported parameter in the first place.

### 3. OPUS: Occupancy Prediction Using a Sparse Set
`arXiv 2409.09350`, NeurIPS 2024

Reformulates occupancy prediction as set prediction rather than dense voxel classification,
avoiding explicit space modeling and sparsification. One of only three Occ3D-nuScenes checkpoints
available publicly in usable form, and therefore one of our candidate models. We also use it to
establish the scale of published between-method differences, against which our measured
operating-point sensitivity is compared.

### 4. ReliOcc: Towards Reliable Semantic Occupancy Prediction via Uncertainty Learning
`arXiv 2409.18026` (2024)

The most important subtraction in this survey. ReliOcc conducts what it describes as the first
comprehensive evaluation of existing semantic occupancy models from a reliability perspective,
finds that a significant reliability gap persists even where accuracy has closed, and proposes a
plug-and-play scheme combining hybrid voxel uncertainty with mix-up learning. It also devises an
uncertainty-aware calibration strategy that operates in offline mode. Because of this paper we
claim neither the first occupancy calibration work nor the first occupancy reliability evaluation.
Our surviving distinction is narrower: no retraining, no uncertainty module, a strictly post-hoc
scalar operating point, reported jointly against RayIoU and a corridor proxy on scene-disjoint
held-out data.

### 5. OCCUQ: Exploring Efficient Uncertainty Quantification for 3D Occupancy Prediction
`arXiv 2503.10605`, ICRA 2025

Adapts an uncertainty estimation technique to 3D occupancy and dynamically calibrates model
confidence using epistemic uncertainty estimates, evaluated under camera corruptions such as fog
and missing cameras. It subtracts cleanly from our work on mechanism, since it requires an
uncertainty module and a fitted procedure whereas we apply a scalar to existing frozen outputs,
but it confirms that the calibration layer around occupancy is occupied territory.

### 6. Effort-Based Criticality Metrics for Evaluating 3D Perception Errors in Autonomous Driving
`arXiv 2603.28029`, IROS 2026

The closest safety-side work. Argues that criticality metrics such as time-to-collision do not
distinguish the operational consequences of false-positive and false-negative perception errors,
and formulates error-specific effort metrics: False Speed Reduction for phantom detections,
Maximum Deceleration Rate for missed objects under a longitudinal kinematic model, and Lateral
Evasion Acceleration coupled with reachability-based collision timing. Its pipeline operates on
tracker outputs and ground-truth bounding boxes rather than semantic voxels, which is the
representation gap our corridor proxy sits in. The paper is also explicit that such metrics are
not substitutes for closed-loop safety validation, and the same caveat binds our proxy.

### 7. EC-IoU: Orienting Safety for Object Detectors via Ego-Centric Intersection-over-Union
`arXiv 2403.15474` (2024)

Proposes a weighting mechanism that refines IoU so a prediction covering points closer to the ego
agent scores higher. Relevant because it can be used in ordinary evaluation processes to select
detectors with better safety-related performance, not only integrated into a training loss. Our
corridor proxy shares the ego-centric weighting intuition and differs in representation: EC-IoU
weights bounding boxes, we weight voxels inside a kinematically swept corridor.

### 8. Can we Trust Unreliable Voxels? Exploring 3D Semantic Occupancy Prediction under Label Noise
`arXiv 2603.06279`, IROS 2026

Establishes OccNL, described as the first benchmark dedicated to 3D occupancy under
occupancy-asymmetric and dynamic trailing label noise, shows that 2D label-noise learning
strategies collapse in sparse 3D voxel spaces, and proposes DPR-Occ, a label-noise-robust
framework built on dual-source partial label reasoning. Its experiments are on SemanticKITTI, so
it does not automatically validate Occ3D-specific results. It is why we run an explicit
label-noise sensitivity analysis on the corridor result rather than treating reconstructed
occluded-region labels as clean ground truth.

### 9. UniOcc: A Unified Benchmark for Occupancy Forecasting and Prediction in Autonomous Driving
`arXiv 2503.24381`, ICCV 2025

Unifies nuScenes, Waymo, CARLA and OpenCOOD data with 2D and 3D occupancy labels and per-voxel
flow annotations, and incorporates evaluation metrics that do not depend on ground-truth labels.
It pre-empts any broad claim that occupancy evaluation rests on a single unexamined protocol. We
treat its simulator-derived occupancy as a possible secondary source, but not as an automatic
independent check on Occ3D, since the split, output representation and label mapping would each
have to be specified and validated first.

### 10. Diffusion-Based Generative Models for 3D Occupancy Prediction in Autonomous Driving
`arXiv 2505.23115`, ICRA 2025

Reframes occupancy prediction as a generative modeling task using diffusion models that learn the
underlying data distribution and incorporate 3D scene priors, and reports that this outperforms
discriminative approaches especially in occluded or low-visibility regions. This is the reason we
do not pursue the adjacent framing of scoring the occluded region as already occupied. It also
illustrates that occluded-region performance is itself an active reporting axis.

### 11. ALOcc: Adaptive Lifting-Based 3D Semantic Occupancy and Cost Volume-Based Flow Predictions
`arXiv 2411.07725`, ICCV 2025

A recent high-performing vision-based occupancy and flow framework built on an occlusion-aware
adaptive lifting mechanism with depth denoising, jointly optimized prototypes for 3D-2D semantic
consistency, and a BEV-centric cost volume. Included as a current state-of-the-art reference point
for the magnitude of published method-to-method differences on Occ3D-nuScenes.

### 12. DAOcc: 3D Object Detection Assisted Multi-Sensor Fusion for 3D Occupancy Prediction
`arXiv 2409.19972`, IEEE TCSVT

A multi-modal occupancy framework that uses 3D object detection supervision to improve occupancy
performance while keeping a deployment-friendly image backbone and practical input resolution.
Included as the multi-sensor state-of-the-art reference point, and as evidence that occupancy
supervision strategy, not just architecture, is an actively contested axis.
