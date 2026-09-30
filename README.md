# Fingerprint-Matching-via-Gabor-Filters
This project implements a complete Automatic Fingerprint Identification System (AFIS) using Gabor Filters for feature extraction and matching.


**Course:** Digital Image Processing (CSE 438) — Course Project
**Language:** Python 3 | **Environment:** Google Colab / Jupyter Notebook

---

## Table of Contents

- [Overview](#overview)
- [Pipeline](#pipeline)
- [Methodology](#methodology)
- [Results](#results)
- [Project Structure](#project-structure)
- [Installation](#installation)
- [Usage](#usage)
- [Limitations and Future Work](#limitations-and-future-work)
- [Tech Stack](#tech-stack)
- [License](#license)

---

## Overview

This project implements an Automatic Fingerprint Identification System (AFIS) prototype using **Gabor filters**, which are well suited to fingerprints because they respond strongly to the local ridge orientation and frequency of the ridge-valley pattern.

The system processes fingerprint images of three subjects (**Child, Father, Mother**) through every stage of the pipeline, extracts a compact feature vector for each, and compares fingerprints using cosine similarity and Euclidean distance.

## Pipeline

| Step | Stage | Description |
|------|-------|-------------|
| 1 | Load images | Read `.bmp` fingerprints as 8-bit grayscale arrays |
| 2 | Histogram equalization | Manual CDF-based lookup-table mapping to boost ridge contrast |
| 3 | Gaussian smoothing | Suppress acquisition noise (σ = 1.0); PSNR evaluated on a synthetic salt-and-pepper test |
| 4 | Gabor filter bank | 8 orientations (0° to 157.5°), 31×31 kernels, producing response, feature and orientation maps |
| 5 | Adaptive binarization | Gaussian adaptive thresholding (block size 25, C = 5), compared against a global threshold |
| 6 | Morphological cleanup | Erosion, dilation and skeletonization (thinning) of ridges |
| 7 | Feature extraction | Block-wise mean Gabor energy on an 8×8 grid across 8 orientations, L2-normalized |
| 8 | Matching | Cosine similarity and Euclidean distance against a decision threshold |
| 9 | Evaluation | Score distributions, FAR/FRR curves, ROC curve and EER |

## Methodology

**Gabor filter bank.** Eight filters at orientations θ = kπ/8 (k = 0…7) are applied to each smoothed image. The magnitude of the response is computed per orientation, then the maximum across orientations forms the *Gabor feature map* and the argmax forms the *orientation map*.

| Parameter | Value |
|-----------|-------|
| Kernel size | 31 × 31 |
| σ (sigma) | 4.0 |
| λ (wavelength) | 10.0 |
| γ (aspect ratio) | 0.5 |
| Orientations | 8 |

**Feature vector.** Each response is divided into an 8 × 8 grid of blocks, and the mean energy of every block is recorded. This gives 8 orientations × 64 blocks = **512 features**, which are L2-normalized.

**Matching.** Two fingerprints are declared a match when their cosine similarity is greater than or equal to the threshold (default **0.99**). Euclidean distance is reported alongside as a secondary measure.

## Results

Output of the matching step (threshold = 0.99):

| Pair | Cosine Similarity | Euclidean Distance | Verdict |
|------|------------------:|-------------------:|---------|
| Child ↔ Child (self-match sanity check) | 1.000000 | 0.000000 | Match |
| Child ↔ Mother | 0.984961 | 0.173430 | No match |
| Father ↔ Mother | 0.979763 | 0.201183 | No match |

The self-match returns a perfect score, and the cross-subject pairs fall below the threshold, as expected for distinct fingerprints.

## Project Structure

```
.
├── Fingerprint_Matching_Final.ipynb   # Full pipeline notebook
├── data/                              # Place fingerprint images here (not included)
│   ├── child.bmp
│   ├── father.bmp
│   └── mother.bmp
├── requirements.txt
└── README.md
```

## Limitations and Future Work

- **Small dataset.** Only three fingerprint images were used, so the results are a demonstration rather than a statistically meaningful evaluation.
- **Simulated ROC analysis.** The FAR/FRR/ROC/EER step (Step 11) uses *synthetically generated* genuine and impostor score distributions to illustrate the methodology. The reported EER therefore does not reflect real-world performance.
- **Global descriptor.** Block-wise Gabor energy is sensitive to translation and rotation. Adding core-point alignment or minutiae-based matching (ridge endings and bifurcations) would improve robustness.
- **Threshold selection.** The 0.99 threshold was chosen manually; it should be tuned on a labelled dataset.
- **Next steps:** evaluate on public benchmarks such as FVC2002/FVC2004 or SOCOFing, add orientation-field-based Gabor enhancement, and report genuine EER on real scores.

This project is released under the [MIT License](LICENSE). It was developed for academic purposes.

## Author

**Fariha Khandaker Moon** — [GitHub](https://github.com/farihamoon))
