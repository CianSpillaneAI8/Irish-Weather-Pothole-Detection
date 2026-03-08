# 🚧 Irish Pothole Detection under Adverse Conditions
### Exploring the viability of synthetic images to improve pothole detection

> **BTYSTE 2025** · Cian Spillane · Gaelcholáiste Carraig Uí Léighin · Preprint in progress(stay tuned)

---

## 📌 Overview

Potholes cost Ireland **€56 million per year** in repairs alone — yet most automated detection systems fail badly in the adverse weather conditions that define the Irish climate. This project proposes a novel approach: using **image-to-image translation** to generate synthetic training data (rain, fog, low-light) and training a **YOLOv7-D6** object detector on a hybrid dataset of real and synthetic pothole images.

The result is a real-time pothole detector specifically suited to Irish weather, achieving a **4.6% mAP improvement** over a non-synthetic baseline — with an **8.6% improvement** on wet/damp conditions and a **31.3% improvement** on stereo images.

---

## 🗂️ Repository Structure

```
irish-pothole-detection/
│
├── notebooks/
│   ├── 1_fog_generation/
│   │   └── fog_cyclegan_inference.ipynb        # Foggy-CycleGAN: clear → foggy pothole images
│   │
│   ├── 2_lowlight_generation/
│   │   ├── day2night_unit_vgg.ipynb            # Day2Night UNIT/VGG: clear → night images (v1)
│   │
│   └── 3_detection/
│       ├── yolov7_training.ipynb               # YOLOv7-D6 training on real/synthetic/hybrid datasets
│       └── yolov11_training_and_testing.ipynb  # YOLOv11 fine-tuning + annotation conversion utilities
│
├── configs/
│   └── data.yaml                               # YOLO dataset config (paths, classes)
│
├── results/
│   ├── baseline/                               # Metrics for model trained on real images only
│   ├── synthetic/                              # Metrics for model trained on synthetic images only
│   └── hybrid/                                 # Metrics for hybrid model (best overall)
│
├── samples/
│   ├── clear/                                  # Example input pothole images
│   ├── foggy/                                  # Example synthetic fog outputs
│   ├── night/                                  # Example synthetic night outputs
│   └── rain/                                   # Example synthetic rain outputs
│
├── requirements.txt
└── README.md
```

---

## 🧠 Methodology

The project is split into two main phases:

### Phase 1 — Synthetic Image Generation

Three types of adverse-condition images were generated from a base pothole dataset using state-of-the-art image-to-image translation models. Conditions were chosen in consultation with **Met Éireann meteorologist Mark Bowe** and **research scientist Eoin Walsh**.

| Condition | Model Used | Images Generated | Source Repo |
|-----------|-----------|-----------------|-------------|
| 🌫️ Fog | Foggy-CycleGAN (Ghais Zaher) | 256 | [ghaiszaher/Foggy-CycleGAN](https://github.com/ghaiszaher/Foggy-CycleGAN) |
| 🌙 Low Light | Day2Night UNIT/VGG16 (Dima Goncharenko) | 256 | [solesensei/day2night](https://github.com/solesensei/day2night) |
| 🌧️ Rain | Custom CycleGAN (trained from scratch) | 74 | [junyanz/pytorch-CycleGAN-and-pix2pix](https://github.com/junyanz/pytorch-CycleGAN-and-pix2pix) |

### Phase 2 — Object Detection

Three YOLOv7-D6 models were trained using transfer learning from COCO weights and evaluated across four diverse test sets:

| Dataset | What it tests |
|---------|--------------|
| Cranfield (300/400/500) | Clear conditions, varied camera distances |
| Dublin | Real Irish potholes — daylight & low-light |
| Stereo/Non-Stereo | Generalisation to unseen image types |
| Annotated Water Dataset | Wet & damp conditions |

---

## 📊 Key Results

| Model | mAP@0.5 (all data) | mAP@0.5 (wet conditions) | mAP@0.5 (stereo) |
|-------|-------------------|--------------------------|------------------|
| Baseline (real only) | 0.763 | 0.650 | 0.640 |
| Synthetic only | 0.715 | 0.715 | 0.929 |
| **Hybrid (real + synthetic)** | **0.809** | **0.736** | **0.953** |

> The hybrid model runs at **44 FPS** using YOLOv7-D6 — well above the 30 FPS real-time threshold.

---

## 🧾 Datasets Used

### Training
| Dataset | Images | Type |
|---------|--------|------|
| [2019 Annotated Pothole Dataset](https://www.kaggle.com/datasets/aryashah2k/potholenet) | 256 | Real |
| [Pothole Detection Dataset](https://github.com/sekilab/RoadDamageDetector) | 272 | Real |
| Synthetic Fog (generated) | 256 | Fake |
| Synthetic Low-Light (generated) | 256 | Fake |
| Synthetic Rain (generated) | 74 | Fake |

### Testing
| Dataset | Images | Conditions |
|---------|--------|-----------|
| Cranfield (TU Dublin) | 150 | Clear, varied distances |
| Dublin (TU Dublin) | 80 | Daylight + low-light |
| Stereo/Non-Stereo (TU Dublin) | 75 | Mixed image types |
| Annotated Water & Dry Potholes | 150 | Wet / damp conditions |

---

## ⚙️ Setup & Usage

### Requirements
```bash
pip install -r requirements.txt
```

### Running the Notebooks

All notebooks are designed to run on **Google Colab** with a GPU runtime (A100 recommended).

1. **Generate foggy images** → `notebooks/1_fog_generation/fog_cyclegan_inference.ipynb`
2. **Generate low-light images** → `notebooks/2_lowlight_generation/day2night_unit_vgg.ipynb`
3. **Train detection model** → `notebooks/3_detection/yolov7_training.ipynb`
4. **Test & evaluate** → `notebooks/3_detection/yolov11_training_and_testing.ipynb`

Mount your Google Drive and update the dataset paths in each notebook before running.

---

## 📦 Model Architecture

- **Object Detector:** YOLOv7-D6
- **Pre-training:** COCO (328,000 images, transfer learning)
- **Image resolution:** 820 × 820
- **Training epochs:** 100
- **GPU:** NVIDIA A100 (Google Colab Pro)

---

## 🙏 Acknowledgements

- **Ghais Zaher** — Foggy-CycleGAN model weights and implementation guidance
- **Dima Goncharenko** — Day2Night UNIT/VGG16 implementation
- **Mark Bowe** (Met Éireann) — Advice on Irish weather conditions
- **Eoin Walsh** (Met Éireann) — Advice on low-light conditions
- **WongKinYiu** — YOLOv7 repository

---

* feel free to reach out if you have any questions, suggestions or run into any problems with it *
