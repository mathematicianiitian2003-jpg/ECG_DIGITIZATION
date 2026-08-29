# 🫀 ECG Image Digitization

Reconstructing 12-lead ECG time-series signals from paper/scanned/photographed ECG images, including images degraded by scanning artifacts, camera distortion, staining, and mold — based on the PhysioNet ECG Image Digitization dataset.

## Problem Statement

Millions of ECGs worldwide exist only as printed or scanned images rather than raw digital signals, making them unusable for automated analysis, machine learning, or longitudinal patient monitoring. This project tackles **ECG digitization**: given an image of a 12-lead ECG (potentially degraded by real-world conditions such as photography, scanning, staining, or mold damage), reconstruct the underlying digital time-series signal (voltage vs. time) for each of the 12 leads.

This is fundamentally an **inverse problem** — recovering a continuous 1D signal from a noisy 2D pixel representation — and combines classical signal processing, image processing, and calibration against a known physical grid (ECG paper standard: 1 mm = 0.04 s horizontally, 1 mm = 0.1 mV vertically).

## Dataset

- **Source:** [PhysioNet ECG Image Digitization](https://www.kaggle.com/competitions) (Kaggle)
- **Training samples:** 12-lead ECG waveforms (I, II, III, aVR, aVL, aVF, V1–V6) with ground-truth CSV signals at sampling frequencies of 250, 500, or 1000 Hz
- **Images:** ~2200 × 1700 px, 9 quality variants per sample:

| Code | Description | Challenge |
|------|-------------|-----------|
| 0001 | Original synthetic image | Clean baseline |
| 0003 | Printed & scanned (color) | Scanning artifacts |
| 0004 | Printed & scanned (B&W) | Loss of color info |
| 0005 | Mobile photo (color print) | Camera distortion, lighting |
| 0006 | Mobile photo (screen) | Moiré patterns, glare |
| 0009 | Stained & soaked prints | Physical damage, stains |
| 0010 | Extensively damaged | Severe degradation |
| 0011 | Mold (color) | Biological damage |
| 0012 | Mold (B&W) | Severe quality loss |

- **Layout convention:** Lead II is shown as a full 10-second rhythm strip; all other 11 leads show only 2.5-second synchronized segments.
- **Evaluation metric:** Modified Signal-to-Noise Ratio (SNR, dB) between predicted and ground-truth signals.

## Approach

1. **Exploratory Data Analysis**
   - Sampling frequency and signal length distribution
   - 12-lead waveform visualization and inter-lead correlation
   - Time-domain vs. frequency-domain (FFT) analysis of representative leads
   - Image property analysis across quality variants (intensity, resolution, degradation type)

2. **Grid Detection & Calibration**
   - Canny edge detection + probabilistic Hough transform to detect the ECG grid lines
   - Horizontal/vertical projection profiling to identify grid spacing
   - Mapping pixel coordinates to physical units (time in seconds, voltage in mV) using the standard ECG grid scale

3. **Signal Extraction**
   - Isolating each lead's trace region from the image layout
   - Tracing the waveform pixel path per column to recover voltage values
   - Denoising / gap-filling for broken or degraded traces

4. **Robustness Across Image Quality**
   - Preprocessing tailored to each degradation type (contrast normalization for scans, deskewing for photos, stain/mold artifact suppression)
   - Evaluating extraction quality across all 9 image variants, not just the clean baseline

5. **Evaluation**
   - Comparing reconstructed signals against ground truth using the competition's Modified SNR metric
   - Per-lead and per-quality-variant performance breakdown

## Results

*(Fill in once the pipeline is complete)*

| Image Variant | Mean SNR (dB) | Notes |
|---|---|---|
| Original (clean) | — | |
| Scanned | — | |
| Mobile photo | — | |
| Damaged/stained | — | |
| Mold | — | |

## Tech Stack

- **Signal & numerical processing:** NumPy, SciPy (FFT, signal module), Pandas
- **Image processing:** OpenCV, PIL
- **Visualization:** Matplotlib, Seaborn, Plotly

## Repository Structure

```
ecg-digitization/
├── ECG_digitalization.ipynb   # Main notebook: EDA, grid detection, signal extraction, evaluation
├── README.md
└── requirements.txt
```

## Key Takeaways

- ECG digitization is a real-world signal recovery problem requiring calibration against a physical measurement grid, not just standard image classification/detection.
- Performance varies substantially with image degradation type — clean synthetic images are far easier to digitize accurately than mobile photos or physically damaged prints, which is the core challenge this project addresses.
- The approach blends classical image processing (edge/line detection, projection analysis) with signal processing (FFT, denoising) rather than relying purely on end-to-end deep learning.


