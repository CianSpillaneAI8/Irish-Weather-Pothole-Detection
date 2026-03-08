# 🚧🦺  Synthetic Image Augmentation for Pothole Detection Under Adverse Irish Weather Conditions

> Exploring the viability of synthetic images to improve pothole detection under adverse conditions — towards an efficient, real-time pothole detector specifically suited to the Irish climate.

**Author:** Cian Spillane — Gaelcholáiste Carraig Uí Leighin  
**Competition:** BT Young Scientist and Technology Exhibition 2025  
**Preprint:** *ArXiv submission in progress*

---

## 📋 Overview

Standard pothole detection models trained on clear-weather data degrade significantly under adverse conditions such as rain, fog, and low light. This is a critical problem for Ireland, where such conditions are frequent — 60% of Irish drivers report vehicle damage from potholes, and Ireland spends an estimated €56 million annually on pothole repair alone.

This project proposes a novel **hybrid dataset approach** combining real pothole images with synthetic weather-augmented counterparts generated via state-of-the-art image-to-image translation models (CycleGAN, UNIT). Three models are trained and rigorously compared — a **baseline** (real images only), **synthetic** (augmented images only), and **hybrid** (combined) — using YOLOv7-D6 with transfer learning.

The hybrid approach achieves a **4.6% overall mAP improvement** over the baseline, with substantially larger gains under wet, low-light, and stereo imaging conditions most relevant to Irish road infrastructure.

---

## 🎯 Key Results

### Overall Performance (455-image diverse test set)

| Model | Precision | Recall | mAP@0.5 |
|-------|-----------|--------|---------|
| Baseline (real images only) | 0.845 | 0.710 | 0.763 |
| Synthetic (augmented only) | 0.757 | 0.714 | 0.715 |
| **Hybrid (real + synthetic)** | **0.779** | **0.774** | **0.809** |

### Performance on Wet / Damp Conditions (Irish-Relevant)

| Model | Precision | Recall | mAP@0.5 |
|-------|-----------|--------|---------|
| Baseline | 0.788 | 0.640 | 0.650 |
| Synthetic | 0.757 | 0.714 | 0.715 |
| **Hybrid** | **0.767** | **0.684** | **0.736** |

> **+8.6% mAP gain** over baseline on wet/damp conditions — the most safety-critical scenario for Irish roads.

### Performance on Stereo Images (Generalisation Test)

| Model | Precision | Recall | mAP@0.5 |
|-------|-----------|--------|---------|
| Baseline | 0.760 | 0.760 | 0.640 |
| Synthetic | 0.920 | 0.920 | 0.929 |
| **Hybrid** | **1.000** | **0.920** | **0.953** |

> **+31.3% mAP gain** over baseline — demonstrating that synthetic augmentation significantly improves generalisation to unseen image types.

### YOLOv7 Architecture Selection

| Model | mAP@0.5 | Inference Speed |
|-------|---------|----------------|
| YOLOv7 | 0.778 | 4.9 ms |
| YOLOv7-X | 0.779 | 6.2 ms |
| YOLOv7-W6 | 0.566 | 8.0 ms |
| YOLOv7-E6 | 0.512 | 9.4 ms |
| **YOLOv7-D6** | **0.819** | **7.0 ms** |
| YOLOv7-E6E | 0.782 | 7.4 ms |

> YOLOv7-D6 outperforms the nominally more powerful E6E variant in **both accuracy and speed** — a counterintuitive finding specific to pothole detection tasks. All models run well above the 30 FPS threshold required for real-time deployment.

---

## 🏗️ Methodology

### Pipeline Overview

```
Real Pothole Images
        │
        ├──► CycleGAN (Fog Translation) ──────────► Synthetic Fog Images
        │
        ├──► UNIT Day2Night (VGG16) ───────────────► Synthetic Low-Light Images
        │
        └──► Custom CycleGAN (Rain Translation) ──► Synthetic Rain Images
                                                            │
                                        ┌───────────────────┘
                                        ▼
                            Hybrid Dataset (802 images)
                                        │
                                        ▼
                          YOLOv7-D6 + Transfer Learning
                                        │
                                        ▼
                            Irish-Climate Pothole Detector
```

### Synthetic Image Generation

Three adverse conditions were selected in consultation with **Met Éireann meteorologist Mark Bowe** and **Met Éireann research scientist Eoin Walsh**:

| Condition | Model Used | Images Generated |
|-----------|-----------|-----------------|
| Fog | Foggy CycleGAN (Zaher, Univ. of Debrecen) | 256 |
| Low Light / Night | Day2Night UNIT — VGG16 (BDD100k + NEXET) | 256 |
| Rain | Custom CycleGAN trained from scratch (Rain100 dataset) | 74 |
| **Total** | | **586 synthetic images** |

### Dataset Composition

| Dataset | Images | Type |
|---------|--------|------|
| 2019 Annotated Pothole Dataset (Kaggle) | 256 | Real |
| Pothole Detection Dataset (GitHub) | 272 | Real |
| Synthetic Fog | 100 | Fake |
| Synthetic Low Light | 100 | Fake |
| Synthetic Rain | 74 | Fake |
| **Hybrid Total** | **802** | **Mixed** |

### Testing Datasets

| Dataset | Images | Conditions Covered |
|---------|--------|--------------------|
| Cranfield (300/400/500) | 150 | Clear, varied distances |
| Dublin Roads | 80 | Daylight + low light |
| Stereo / Non-Stereo | 75 | Novel image types |
| Annotated Water/Dry | 150 | Wet and damp conditions |
| **Total** | **455** | |

---

## 🛠️ Technical Stack

- **Object Detection:** YOLOv7-D6 (PyTorch)
- **Image Translation:** CycleGAN, UNIT
- **Training Environment:** Google Colab Pro (A100 GPU)
- **Transfer Learning:** COCO dataset (328,000 images)
- **Image Resolution:** 820 × 820 pixels
- **Training Epochs:** 100

---

## 📊 Key Findings

1. **Hybrid datasets outperform both pure real and pure synthetic approaches** across nearly all test conditions, confirming the core hypothesis.

2. **Synthetic augmentation most valuable under adverse conditions** — the largest gains occur precisely in the wet, low-light, and novel-image scenarios most relevant to Irish roads.

3. **YOLOv7-D6 is the optimal architecture for pothole detection** — counterintuitively outperforming the larger E6E variant in both accuracy and inference speed.

4. **Synthetic images improve generalisation** — the 31.3% stereo image improvement demonstrates that weather augmentation enhances model robustness beyond just the trained conditions.

5. **Dublin road images remain a persistent challenge** — even the hybrid model struggles to exceed 0.70 mAP on Dublin potholes, suggesting this warrants dedicated future work.

---

## 🔭 Future Work

- Training on larger datasets (BDD100k scale) with sufficient compute
- Investigating optimal synthetic-to-real image ratios beyond the 70/30 split used here
- Dedicated Dublin road pothole dataset collection
- Exploration of YOLOv8/v11 architectures under adverse conditions
- Semantic segmentation approach comparison

---

## 📁 Repository Structure

```
├── data/
│   ├── training/          # Training dataset (real images + annotations)
│   ├── synthetic/         # Generated synthetic images (fog, night, rain)
│   └── testing/           # Test datasets (Cranfield, Dublin, Stereo, Water)
├── models/
│   ├── fog_cyclegan/      # Foggy CycleGAN implementation
│   ├── day2night/         # UNIT day-to-night translation
│   └── rain_cyclegan/     # Custom rain CycleGAN
├── yolov7/                # YOLOv7 training scripts
├── notebooks/             # Google Colab training notebooks
├── results/               # Evaluation metrics and result tables
└── README.md
```

---

*If you found this work useful or have questions about the methodology, feel free to open an issue or reach out.*
