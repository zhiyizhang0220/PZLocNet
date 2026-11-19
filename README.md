# PZLocNet

**PZLocNet: A task-oriented lightweight crop localization network for protection zone-based robotic intra-row maize weeding**

This repository provides research resources associated with PZLocNet, a task-oriented lightweight maize seedling localization network developed for protection zone-based robotic intra-row weeding.

PZLocNet systematically reconstructs the YOLO11n architecture to balance whole-seedling localization accuracy, model compactness, cross-domain generalization, and embedded deployment efficiency. The proposed network integrates edge-aware shallow feature extraction, global–local contextual interaction, efficient multiscale feature fusion, shared-convolution prediction, and training-only localization-quality-aware sample assignment.

The evaluation extends from offline detection and cross-domain transfer to embedded deployment and preliminary closed-loop robotic weeding under actual field conditions.

## Repository status

- **Model name:** PZLocNet.
- **Manuscript status:** Pre-publication.
- **Journal article DOI:** Not yet available.
- **Earlier preprint:** EAGLE-YOLO, available on SSRN at https://doi.org/10.2139/ssrn.6864585.
- The current manuscript is a revised and expanded version of the earlier preprint.
- The manuscript `.docx` file is intentionally excluded from version control.
- Large images, videos, spreadsheets, and model weights are tracked using **Git LFS**.

## Main contributions

- **EAS-Stem (Edge-aware saliency stem):** Preserves shallow directional gradients and local saliency cues associated with narrow maize leaves.
- **GLIB (Global–local interactive backbone):** Integrates asymmetric dual-stream feature extraction and bidirectional feature fusion for complementary local-detail and contextual representations.
- **EMBS-FPN (Efficient multibranch and scale feature pyramid network):** Combines normalized weighted fusion, level-specific multiscale convolution kernels, and shallow feature reinjection.
- **LSCD (Lightweight shared-convolution detection head):** Reduces prediction-head redundancy through cross-level parameter sharing.
- **PowerAlign:** Applies a piecewise power transformation to geometric localization quality during task-aligned positive sample assignment, without introducing additional inference operations.

The proposed framework is evaluated through component ablation, detector benchmarking, compound field perturbation augmentation, cross-site transfer, external-domain evaluation, robot-camera validation, embedded deployment, and preliminary closed-loop field trials.

## Repository structure

```text
.
|-- dataset/                # YOLO-format maize seedling images and labels
|-- visualization/          # Detection visualizations and qualitative outputs
|-- benchmark_results/      # Comparison experiments with representative detectors
|-- ablation_studies/       # Model-component and sensitivity analysis results
|-- dataset_ablation/       # Compound field perturbation augmentation experiments
|-- generalization_tests/   # Cross-site, cross-dataset, and cross-camera evaluations
|-- docs/                   # Dataset, model, ethics, and reproduction documentation
|-- metadata/               # Generated manifests and integrity metadata
|-- Field Trial Data.xlsx   # Closed-loop robotic intra-row weeding trial data
`-- Field Trial Video.mp4   # Representative field trial video record
```

## Dataset summary

### 1. Nikon Z5 source-domain dataset

The collected near-ground RGB maize seedling dataset contains **3,160 original images** and **14,585 annotated maize seedling instances**.

Images were acquired at two agricultural sites in China.

| Collection site | Cultivar | Images | Annotated instances |
|---|---|---:|---:|
| Yangling, Shaanxi | Shandan 650 | 2,225 | 10,246 |
| Xingtang, Hebei | Nongda 108 | 935 | 4,339 |
| Total | — | 3,160 | 14,585 |

Key acquisition conditions:

- **Camera:** Nikon Z5 with a 25–50 mm f/4–6.3 lens.
- **Camera configuration:** Top-down imaging, approximately 0.8 m above the soil surface.
- **Image resolution:** 1920 × 1080 pixels.
- **Collection periods:** June 2024 in Yangling and July 2025 in Xingtang.
- **Daily collection windows:** 08:00–10:00, 12:00–14:00, and 17:00–19:00.
- **Annotation:** Single-class, axis-aligned YOLO bounding boxes enclosing whole visible maize seedlings.

The original dataset was partitioned into mutually plot-disjoint training, validation, and test splits.

| Dataset split | Original images | Images after augmentation |
|---|---:|---:|
| Training | 2,212 | 8,848 |
| Validation | 474 | 474 |
| Test | 474 | 474 |
| Total | 3,160 | 9,796 |

Compound field perturbation augmentation was applied exclusively to the training split. Validation and test images remained unaugmented.

### 2. Independent Gemini335 robot-camera dataset

An independent onboard RGB dataset was collected using an Orbbec Gemini335 stereo camera prior to the closed-loop field trials.

- **Camera:** Orbbec Gemini335.
- **Mounting height:** Approximately 0.4 m above the soil surface.
- **Images:** 300.
- **Annotated maize seedling instances:** 765.
- **Video sampling interval:** 2,000 ms.
- **Evaluation protocol:** Zero-shot sensor and viewpoint transfer without onboard retraining or confidence threshold tuning.

This dataset was reserved exclusively for independent robot-camera evaluation.

### 3. External-domain datasets

Maize-related annotations from two public datasets were adopted for external-domain evaluation.

| Dataset | Selected images | Training | Validation | Test |
|---|---:|---:|---:|---:|
| CornWeed Dataset (CWD) | 1,878 | 1,278 | 300 | 300 |
| Vegetable Crops Dataset (VCD) | 1,045 | 725 | 160 | 160 |

The external-domain experiments included zero-shot evaluation and limited-label fine-tuning using 10%, 20%, and 30% of the corresponding target-domain training splits.

The original third-party datasets remain subject to their respective data-use and licensing terms.

See [`docs/DATASET_CARD.md`](docs/DATASET_CARD.md) for additional dataset information.

## Key reported results

### 1. Source-domain detection performance

On the plot-disjoint Nikon Z5 test set, PZLocNet achieved improved localization accuracy and reduced model complexity relative to YOLO11n.

| Metric | YOLO11n | PZLocNet |
|---|---:|---:|
| Precision (%) | 96.12 | 95.79 |
| Recall (%) | 95.24 | **97.01** |
| F1 score (%) | 95.68 | **96.40** |
| mAP50 (%) | 98.21 | **99.00** |
| mAP75 (%) | 79.08 | **81.82** |
| mAP50:95 (%) | 68.00 | **71.51** |
| Parameters (M) | 2.58 | **0.73** |
| GFLOPs | 6.3 | **2.8** |
| Serialized weight size (MB) | 5.2 | **2.0** |
| Desktop GPU throughput (FPS) | 177.2 | **204.5** |

Compared with YOLO11n, PZLocNet achieved:

- **+1.77 percentage points** in recall.
- **+2.74 percentage points** in mAP75.
- **+3.51 percentage points** in mAP50:95.
- **71.7% reduction** in model parameters.
- **55.6% reduction** in theoretical computational cost.
- **61.5% reduction** in serialized weight size.

PowerAlign alone improved the baseline mAP50:95 by 2.04 percentage points without introducing additional inference operations.

### 2. Zero-shot cross-domain generalization

The following table summarizes mean mAP50:95 under different unseen-domain evaluation protocols.

| Evaluation domain | YOLO11n (%) | PZLocNet (%) | Improvement (pp) |
|---|---:|---:|---:|
| Yangling → Xingtang | 57.41 | **57.62** | +0.21 |
| Xingtang → Yangling | 46.63 | **50.14** | +3.51 |
| CWD | 54.85 | **56.30** | +1.45 |
| VCD | 40.05 | **44.95** | +4.90 |
| Gemini335 robot-camera dataset | 62.17 | **68.58** | +6.41 |

PZLocNet achieved higher mean mAP50:95 than YOLO11n across all five zero-shot evaluation settings, although substantial domain gaps remained.

Limited-label fine-tuning was further investigated to assess adaptation to new field and imaging environments. Complete experimental results and evaluation materials are provided in the corresponding repository folders.

### 3. Embedded deployment

PZLocNet was deployed on an NVIDIA Jetson Orin NX using FP16 TensorRT optimization.

| Video stream | Total frames | YOLO11n (ms/frame) | PZLocNet (ms/frame) |
|---|---:|---:|---:|
| Stream 1 | 5,120 | 12.2 ± 1.2 | 12.1 ± 0.7 |
| Stream 2 | 4,860 | 12.0 ± 1.5 | 11.9 ± 1.7 |
| Stream 3 | 4,960 | 12.4 ± 0.8 | 12.0 ± 1.2 |

PZLocNet maintained approximately **12 ms system-level video processing latency per frame**, corresponding to a processing throughput of approximately **83 FPS**.

System-level latency includes frame decoding, host-to-device memory transfer, image preprocessing, TensorRT execution, and non-maximum suppression. It should not be interpreted as isolated neural network inference latency.

### 4. Preliminary closed-loop field trials

PZLocNet was integrated into a protection zone-based robotic intra-row maize weeding system equipped with an onboard Gemini335 camera, a Jetson Orin NX computing unit, and symmetrically actuated opening–closing weeding knives.

Preliminary field trials were conducted across six 10-m plots at a forward travel speed of 0.2 m/s, with three alternating plots per detector.

| Evaluation metric | YOLO11n | PZLocNet |
|---|---:|---:|
| Crop damage rate (%) | 18.47 ± 5.73 | **7.59 ± 2.15** |
| Weeding success rate (%) | 77.78 ± 4.96 | **89.99 ± 1.58** |
| Missed weeding rate (%) | 22.22 ± 4.96 | **10.01 ± 1.58** |
| Invalid actuation rate (%) | 37.64 ± 5.52 | **8.41 ± 4.78** |
| Perception-to-control-command latency (ms) | 16.73 ± 0.45 | 16.63 ± 0.47 |

Under the tested conditions, PZLocNet showed a reduction of 10.88 percentage points in the mean crop damage rate, an increase of 12.21 percentage points in the mean weeding success rate, and a reduction of 29.23 percentage points in the mean invalid actuation rate.

The reported field metrics are plot-level mean ± standard deviation. Perception-to-control-command latency excludes subsequent mechanical actuator movement.

These preliminary results support the practical feasibility of compact whole-seedling localization for protection zone-based robotic maize weeding. Broader validation across operating speeds, soil conditions, weed densities, and maize growth stages remains necessary.

## Git LFS requirement

Large research files in this repository are managed using Git Large File Storage (Git LFS).

Before cloning or downloading the complete repository, install Git LFS and execute:

```bash
git lfs install
git clone https://github.com/zhiyizhang0220/PZLocNet.git
cd PZLocNet
git lfs pull
```

To initially clone the repository without downloading large LFS-managed files, use:

```bash
GIT_LFS_SKIP_SMUDGE=1 git clone https://github.com/zhiyizhang0220/PZLocNet.git
cd PZLocNet
```

Individual LFS-managed directories can subsequently be downloaded as needed:

```bash
git lfs pull --include="dataset/**"
```

Due to the size of the datasets, model checkpoints, experimental outputs, and field videos, sufficient local storage and network bandwidth are required. Git LFS downloads may also be subject to GitHub storage and bandwidth limits.

## Experimental environment

The principal training and deployment configurations reported in the manuscript are summarized below.

### Training environment

| Configuration | Setting |
|---|---|
| Deep learning framework | PyTorch 2.7.0+cu128 |
| Desktop GPU | NVIDIA GeForce RTX 5080 |
| Input resolution | 640 × 640 |
| Training epochs | 150 |
| Batch size | 16 |
| Optimizer | SGD |
| Initial learning rate | 0.01 |
| Random seeds | 2026, 2027, 2028 |
| Desktop inference precision | FP16 |
| Inference batch size | 1 |

### Embedded deployment environment

| Configuration | Setting |
|---|---|
| Embedded platform | NVIDIA Jetson Orin NX |
| Power mode | Maximum-power mode |
| Model export format | ONNX |
| Inference engine | TensorRT 8.5.2.2 |
| Inference precision | FP16 |

Unless otherwise specified, quantitative detection results in the manuscript are reported as mean ± standard deviation across three independent runs.

The repository includes experimental assets from YOLO-family and MMDetection-style experiments. Model-specific default hyperparameters are retained where applicable.

A minimal environment template is provided in [`environment.yml`](environment.yml). Additional model-specific dependencies and configurations should be checked against the corresponding experimental records.

## Reproduction notes

This repository organizes research resources associated with:

- Original datasets and YOLO-format annotations.
- Model configurations, source code, and trained checkpoints.
- Detection visualizations and qualitative comparison results.
- Lightweight and heterogeneous detector benchmarks.
- Component ablation and hyperparameter sensitivity analysis.
- Compound field perturbation augmentation experiments.
- Cross-site, external-domain, and robot-camera evaluations.
- Embedded deployment and closed-loop field trial results.
- Experimental spreadsheets, metadata, and supplementary videos.

For additional information, refer to:

- [`docs/REPRODUCIBILITY.md`](docs/REPRODUCIBILITY.md)
- [`docs/MODEL_CARD.md`](docs/MODEL_CARD.md)
- [`docs/DATASET_CARD.md`](docs/DATASET_CARD.md)
- [`docs/FIELD_TRIAL.md`](docs/FIELD_TRIAL.md)
- [`docs/DATA_AVAILABILITY.md`](docs/DATA_AVAILABILITY.md)

## Data availability

The research resources associated with PZLocNet are hosted at:

https://github.com/zhiyizhang0220/PZLocNet

The third-party CornWeed Dataset (CWD) and Vegetable Crops Dataset (VCD) used for cross-domain evaluation are available from their original sources.

Only maize-related annotations were retained for the corresponding experiments. Users should consult the original dataset providers for licensing, attribution, redistribution, and reuse requirements.

## Citation

If you use PZLocNet or its associated research resources, please cite the corresponding manuscript.

**Zhiyi Zhang, Zhuohuai Guan, Saisai Liu, Shenghong Wei, Mengdi Luo, Jiahe Tang, Yuxiang Huang, Zhouzhou Zheng, Guangqiao Cao, and Zhiqi Zheng.**

*PZLocNet: A task-oriented lightweight crop localization network for protection zone-based robotic intra-row maize weeding.*

Manuscript, 2026.

A provisional citation file is provided in [`CITATION.cff`](CITATION.cff).

An earlier version of this work, entitled EAGLE-YOLO, was posted as a preprint on SSRN:

https://doi.org/10.2139/ssrn.6864585

The current PZLocNet manuscript is a revised and expanded version of that work. Formal journal citation information will be updated upon publication.

## Acknowledgements

This work was supported by:

- Key R&D Projects of Shaanxi Province (2019NY-175).
- Jiangsu Agricultural Science and Technology Independent Innovation Fund [CX(24)3029].
- Northwest A&F University Independent Discipline Construction Project (Z1011124001).

## License

The repository's original code and documentation are distributed under the MIT License, subject to the terms specified in the repository license file.

Dataset and model-weight reuse is subject to the applicable licensing and data-use terms. Third-party datasets, software components, and pretrained model weights remain subject to their respective original licenses.

See [`docs/DATA_AVAILABILITY.md`](docs/DATA_AVAILABILITY.md) for additional information.

## Contact

For research-related questions, please contact the corresponding authors:

- **Guangqiao Cao:** caoguangqiao@126.com
- **Zhiqi Zheng:** zhiqizheng@nwsuaf.edu.cn

Academic feedback and reproducibility-related questions are welcome through GitHub Issues.
