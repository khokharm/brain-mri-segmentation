# 🧠 Brain MRI Segmentation and Classification

A deep learning pipeline combining **U-Net** (segmentation) and **VGG16** (classification) to automatically detect and classify brain tumors from MRI scans.

> **Authors:** Wasay Aamer, Muzammil Hussain Memon, Mahad Khokhar  
> **Institution:** Department of Electrical and Computer Engineering, Toronto Metropolitan University  
> **Course:** EE 8209 — Intelligent Systems, Toronto Metropolitan University

---

## 📋 Table of Contents

- [Overview](#overview)
- [Results](#results)
- [Dataset](#dataset)
- [Models](#models)
- [Preprocessing](#preprocessing)
- [Project Structure](#project-structure)
- [Installation](#installation)
- [Usage](#usage)
- [Evaluation Metrics](#evaluation-metrics)
- [References](#references)

---

## Overview

Brain tumors are among the most life-threatening forms of cancer. Manual analysis of MRI scans is time-consuming, subjective, and difficult to scale. This project addresses that problem with an automated two-stage deep learning pipeline:

1. **Segmentation** — U-Net produces a pixel-level mask highlighting the tumor region in each MRI slice.
2. **Classification** — A fine-tuned VGG16 classifies the tumor into one of four categories:
   - Glioma
   - Meningioma
   - Pituitary Tumor
   - No Tumor

The models were trained and evaluated on the **BRISC 2025 Kaggle dataset**.

---

## Results

| Task | Model | Metric | Score |
|------|-------|--------|-------|
| Segmentation | U-Net | Dice Coefficient | **0.824** |
| Segmentation | U-Net | IoU (Jaccard Index) | **0.750** |
| Segmentation | U-Net | Binary Accuracy | 0.998 |
| Classification | VGG16 | Overall Accuracy | **94%** |
| Classification | VGG16 | Macro F1-Score | 0.93 |

### Per-Class Classification Performance

| Class | Precision | Recall | F1-Score |
|-------|-----------|--------|----------|
| Glioma | 0.89 | 0.92 | 0.90 |
| Meningioma | — | 0.83 | 0.87 |
| Pituitary | — | — | 0.96 |
| No Tumor | — | — | 0.98 |

---

## Dataset

**BRISC 2025** — Available on [Kaggle](https://www.kaggle.com/)

### Classification Split

| Split | Glioma | Meningioma | Pituitary | No Tumor | Total |
|-------|--------|------------|-----------|----------|-------|
| Train | 1,147 | 1,329 | 1,457 | 1,067 | **5,000** |
| Test | 254 | 306 | 300 | 140 | **1,000** |

### Segmentation Split

| Split | Images | Masks |
|-------|--------|-------|
| Train | 3,933 | 3,933 |
| Test | 860 | 860 |

---

## Models

### U-Net (Segmentation)

U-Net uses a symmetric encoder-decoder architecture:

- **Encoder (contracting path):** Repeated 3×3 convolutions + ReLU activations + 2×2 max-pooling to extract abstract spatial features.
- **Decoder (expansive path):** Up-convolutions to restore spatial resolution and produce a full segmentation mask.
- **Skip connections:** Copy-and-crop connections between encoder and decoder layers preserve high-resolution spatial detail, enabling precise tumor localization.

### VGG16 (Classification)

VGG16 (Visual Geometry Group, 16 layers) is a deep CNN developed at the University of Oxford, consisting of 13 convolutional layers in 5 blocks and 3 fully connected layers.

- **Transfer learning:** Pretrained on ImageNet via PyTorch's `torchvision`.
- **Fine-tuning strategy:** The convolutional base was frozen; only the final fully connected layer was replaced with a new 4-class output head.
- **Training:** 10 epochs, with accuracy improving from 70% (epoch 1) to 96.8% (epoch 10).

---

## Preprocessing

### For U-Net
- Resize to **224×224** pixels
- Convert to **grayscale** (1 channel — appropriate for MRI intensity data)
- Normalize pixel values to **[0.0, 1.0]** by dividing by 255
- Add channel dimension → final shape: `(224, 224, 1)`

### For VGG16
- Resize to **224×224** pixels (matches pretrained model's expected input)
- Convert color channels from **BGR → RGB**
- Apply Keras `preprocess_input()` for **ImageNet mean subtraction** normalization

---

## Project Structure

```
├── Final_project_code.ipynb   # Main notebook: training, evaluation, and visualization
├── Brain_MRI_Final.pptx       # Project presentation slides
├── Final_Report.pdf           # Full IEEE-format project report
├── README.md
└── LICENSE
```

---

## Installation

```bash
# Clone the repository
git clone https://github.com/your-username/brain-mri-segmentation.git
cd brain-mri-segmentation

# Install dependencies
pip install tensorflow keras torch torchvision opencv-python numpy matplotlib scikit-learn
```

> **Note:** A GPU is strongly recommended for training. The project was developed using Google Colab.

---

## Usage

Open and run `Final_project_code.ipynb` in Jupyter or Google Colab.

The notebook covers:
1. Data loading and preprocessing
2. U-Net model definition and training
3. Segmentation mask prediction and visualization
4. VGG16 model definition, fine-tuning, and training
5. Classification evaluation (accuracy, confusion matrix, classification report)
6. Side-by-side comparison of predictions vs. ground truth

---

## Evaluation Metrics

**Segmentation (U-Net)**

$$\text{IoU} = \frac{|A \cap B|}{|A \cup B|}$$

$$\text{Dice} = \frac{2|A \cap B|}{|A| + |B|}$$

Where *A* = predicted mask, *B* = ground truth mask. A Dice score of 1.0 indicates a perfect overlap.

**Classification (VGG16)**

Overall accuracy = proportion of correctly predicted labels across all four tumor categories.

---

## References

1. O. Ronneberger, P. Fischer, and T. Brox, "U-Net: Convolutional Networks for Biomedical Image Segmentation," *MICCAI*, 2015.
2. A. Arafat et al., "Brain Tumor MRI Image Segmentation and Classification based on Deep Learning Techniques," *ICSTSN*, 2023.
3. O. Alirr et al., "Automatic Brain Tumor Segmentation from MRI Scans using U-Net Deep Learning," *IEEE BioSMART*, 2023.
4. S. Ghosh and K. C. Santosh, "Tumor Segmentation in Brain MRI: U-Nets versus Feature Pyramid Network," *IEEE CBMS*, 2021.
5. GeeksforGeeks, "VGG-16 — CNN model." https://www.geeksforgeeks.org/vgg-16-cnn-model/
