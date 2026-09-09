# AI Novelty and Feasibility Audit

**Project:** Operating points in 3D semantic occupancy  
**Course:** CMPE 249, Fall 2026  
**Date:** 2026-09-08

This is the AI evaluation of the project's novelty, red-ocean risk, and feasibility, pasted
as the assignment requires. It is not favourable throughout and the unfavourable parts have
been left in.

**Verdict: NARROW-OPEN, conditional on a feasibility spike.** Scored 2 out of 5 as
originally worded, 3 out of 5 after the narrowing described in section 3, which has been
applied to the submitted proposal.

---

## 1. How this audit was produced

Two independent processes, run separately and then reconciled.

Four adversarial screening agents, each with a default verdict of OCCUPIED, attacking the
idea from a different vocabulary: semantic scene completion and amodal completion;
synthetic occupancy datasets; the visibility mask as an evaluation artifact; and execution
feasibility. Each was required to read primary sources in full and resolve every identifier
before citing it.

Separately, a teammate ran an independent novelty and red-ocean audit under a research
protocol requiring verbatim quotation validation against locally captured sources.

The two processes disagreed in useful places. Where they did, the reconciliation is
recorded in section 7 rather than silently resolved.

---

## 2. Red-ocean assessment

The surrounding field is red and the project deliberately does not enter it. 3D semantic
occupancy prediction is saturated: multiple surveys, a CVPR challenge, a live leaderboard,
and new architectures monthly. This project proposes no new occupancy architecture and
would lose if it did.

The evaluation and calibration layer, however, is not empty either, and an earlier draft
wrongly claimed it was.

| Area | Status | Consequence for the claim |
|---|---|---|
| Occupancy reliability and confidence calibration | Red, occupied | ReliOcc, IJCAI 2025, reports the first comprehensive reliability evaluation of existing occupancy models and provides an offline calibration strategy. OCCUQ, ICRA 2025, dynamically calibrates confidence. We claim neither the first occupancy calibration nor the first reliability benchmark. |
| Unified occupancy evaluation tooling | Red, adjacent | UniOcc is a unified benchmark with label-free metrics. Our tool is not the first occupancy evaluation tool. |
| Label-noise-aware occupancy evaluation | Red, adjacent | An occupancy label-noise benchmark exists. Corridor results must include label-noise sensitivity. |
| Safety-weighted perception evaluation | Adjacent, open | Effort-based criticality metrics and EC-IoU weight errors by driving consequence, but operate on detection and tracking objects rather than semantic voxels. A voxel-level corridor metric can be a contribution, but only as an offline proxy and only with a precise definition. |
| Scoring the camera-occluded region | Occupied | Diffusion-based occupancy work publishes performance in camera-invisible regions and stratifies by visibility. ALOcc and DAOcc run mask-on and mask-off as named benchmarks. This framing was screened and dropped. |
| **Cross-model post-hoc operating-point sensitivity, reported jointly against mIoU, RayIoU, and a corridor proxy on held-out scenes** | **Narrow-open, unproven** | The only claim preserved |

---

## 3. Defects found in our own earlier draft, and the corrections applied

This is the most useful section of the audit. Every item below was wrong in a draft that
was close to being submitted.

| Defect | Correction |
|---|---|
| Called a geometry-changing dilation operator "calibration". Category error. | Split into two labelled arms. Temperature scaling and monotonic calibrators are calibration. Dilation and erosion are an output-perturbation stress test. |
| Assumed heterogeneous checkpoints share one adjustable threshold. Dense models take argmax over roughly 18 classes including a free class, and SparseOcc's standalone evaluator saves predictions as a uint8 semantic grid, so raw scores require modifying inference code. | Made an empirical precondition. The two-day spike must recover raw scores and reproduce each checkpoint's published metric before that checkpoint is eligible. |
| Specified a five-model sweep. Only three usable public Occ3D checkpoints exist: FB-OCC, SparseOcc, and OPUS. BEVFormer has no occupancy checkpoint, since the leaderboard rows are third-party re-implementations with unreleased weights. CTF-Occ was never released. SurroundOcc is gated and uses different ground truth. | Reduced to two or three models, selected after the spike. |
| Would have fitted the operating point on the same frames used for the reported curve. Occ3D-nuScenes has no held-out test split. | Scene-disjoint calibration and test partitions, frozen and hash-stamped before any sweeping. Every reported number comes from held-out scenes. |
| Subtracted ReliOcc as training-time uncertainty. It includes an offline calibration strategy. | Distinction restated precisely: no retraining, no uncertainty module, strictly post-hoc scalar. |
| Claimed EC-IoU's remedy requires retraining. It can be used in evaluation to select detectors with better safety-related performance. | Claim removed. The subtraction is now on representation, boxes versus voxels. |
| Two quotations attributed to a 2026 criticality-metrics paper failed verbatim validation. | Both removed. Only the validated future-work phrase is retained. |
| A test-time occupancy paper cited with the wrong publication year. | Corrected. |
| Cited an occupancy label-noise benchmark as validating Occ3D corridor results. | Its experiments are on SemanticKITTI. Downgraded to motivation for our own label-noise analysis. |
| Treated UniOcc as an automatic independent ground-truth check. | Requires specifying exact split, output representation, and label mapping first. Demoted to a stretch goal. |

---

## 4. Adjacent framings screened and killed

Recorded so they are not re-proposed, and because they show the search was not run only to
confirm a preferred answer.

**Scoring the camera-occluded region of Occ3D.** OCCUPIED. Diffusion-based occupancy work
already publishes performance in camera-invisible regions and stratifies by visibility
probability; ALOcc and DAOcc run mask-off as a named benchmark. Further, the occluded-region
ground truth is produced by multi-frame aggregation and mesh reconstruction, so a result
measured there partly scores the label pipeline rather than the model.

**Simulator-complete ground truth for occluded voxels.** OCCUPIED. This is semantic scene
completion, a task whose evaluation has scored occluded voxels since 2017, and a CVPR 2026
paper already builds the mesh-derived CARLA benchmark and reports that existing methods
drop under faithful occlusions. Also infeasible here: CARLA has no occupancy sensor, so
complete voxel ground truth requires a custom Unreal sensor and a multi-hour source build.

**A runtime perception-assurance monitor evaluated at matched false-intervention rate.**
OCCUPIED. Fixing a false-alarm budget and reporting detection with lead time has been the
standard protocol in that subfield since SelfOracle, ICSE 2020.

---

## 5. Feasibility

| Constraint | Status |
|---|---|
| No physical hardware | Satisfied. Public offline dataset only. |
| 16 GB VRAM | Not binding. Inference only. Measured occupancy inference sits around 4.5 to 6 GB. |
| Blackwell sm_120 toolchain | Real, and designed around. mmcv, mmdet3d, and spconv pin CUDA 11.8 and emit no sm_120 kernels, the pure-PyTorch fallback fires only for CPU tensors, and FB-OCC's pooling operator has no fallback. Mitigation: one inference pass on a rented pre-Blackwell GPU, cache the outputs, analyse locally on CPU. |
| Local to cloud by config change | Satisfied by construction. The GPU is touched once; everything downstream is CPU. |
| Budget | Roughly 1 to 5 dollars of GPU time. Storage dominates. Cache argmax plus top-3, roughly 35 GB, never full float16 logits, which would be roughly 139 GB. |
| 11 weeks, 3 people | Satisfied, with a week-1 kill gate. |
| Open-source artifact | The released cache lets others reproduce the analysis with no GPU. |

---

## 6. Honest weaknesses

1. The claim is conditional on the spike. If checkpoints do not expose a recoverable common
   operating point, the premise fails for those models and scope shrinks to whichever ones
   do.
2. The corridor metric scores against reconstructed labels and therefore partly measures
   agreement with that reconstruction. The label-noise analysis bounds this; it does not
   eliminate it.
3. We expect half the claim to be false. RayIoU should resist the operating point. This is
   pre-registered so that a null result is a finding rather than a failure.
4. Scoop risk is real and dated. A recent safety-weighted perception paper names
   confidence-sweep effort-recall curves as future work.
5. This is a measurement study, not a new architecture and not a safety guarantee.
6. Process limitation. The teammate audit could not complete its protocol's mandatory
   independent review in that session. The four screening agents partially fill that role,
   but both processes share an author's framing of the question. Before the final report,
   an independent reviewer should challenge the narrowed claim and the citation table.

---

## 7. Disagreement between the audit processes, left unresolved

Recorded rather than smoothed over.

The screening agents initially judged the broader occlusion and mask framing OCCUPIED and
recommended abandoning the occupancy area entirely. The teammate audit judged the narrowed
operating-point question NARROW-OPEN and recommended proceeding after a spike. Both are
correct about different claims: the occlusion framing is occupied and has been dropped; the
operating-point framing survives, conditionally, and is what is submitted.

A further disagreement stands unresolved. Our instructor's view is that the mIoU and RayIoU
benchmarks already sweep thresholds. Our reading is that RayIoU sweeps a distance tolerance
and casts rays from eight ego poses, both properties of the metric, whereas this study
varies an operating point on the model's own output scores, a property of the deployed
system. We may be wrong that the distinction matters, and the two-day spike is the cheapest
available test of it. If no common operating point can be defined across checkpoints, the
instructor's objection is vindicated and the project stops in week 1.
