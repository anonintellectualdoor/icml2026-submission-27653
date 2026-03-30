# Supplementary Material for ICML 2026 Submission #27653

**On High-Frequency Collapse in Spectral Vision Transformers**

This repository contains supplementary tables and figures referenced in the author rebuttals.

---

## Table 1: Multi-Seed Evaluation (3 seeds)

### ImageNet-100 (150 epochs)

| Seed | SpectFormer (Top-1 / Top-5) | HF-SpectFormer (Top-1 / Top-5) | Δ Top-1 |
|------|----------------------------|-------------------------------|---------|
| 42 | 82.48 / 95.42 | 83.22 / 96.00 | +0.74 |
| 123 | 82.54 / 95.72 | 84.30 / 96.22 | +1.76 |
| 7 | 82.46 / 95.50 | 83.76 / 95.70 | +1.30 |
| **Mean ± std** | **82.49 ± 0.03** | **83.76 ± 0.44** | **+1.27** |

### ImageNet-100 (100 epochs)

| Seed | SpectFormer (Top-1 / Top-5) | HF-SpectFormer (Top-1 / Top-5) | Δ Top-1 |
|------|----------------------------|-------------------------------|---------|
| 42 | 77.8 / 94.22 | 79.86 / 94.64 | +2.06 |
| 123 | 78.2 / 93.88 | 80.18 / 94.6 | +1.98 |
| 7 | 78.22 / 93.86 | 80.38 / 94.36 | +2.16 |
| **Mean ± std** | **78.07 ± 0.19** | **80.14 ± 0.21** | **+2.07** |

### DTD (100 epochs)

| Model | Test Top-1 (mean ± std) | Test Top-5 (mean ± std) |
|---|---|---|
| SpectFormer | 21.44 ± 1.51% | 49.08 ± 1.94% |
| HF-Skip | 30.27 ± 0.08% | 61.52 ± 0.56% |
| **Δ** | **+8.83** | **+12.44** |

---

## Table 2: Smooth Mask Ablation (DTD)

| Mask | Test Top-1 | Δ vs Hard |
|------|-----------|-----------|
| Hard (paper default) | 30.21% | baseline |
| Cosine (w=0.10) | 30.43% | +0.22 ≈ same |
| Gaussian narrow (σ=0.05) | 28.62% | −1.59 |
| Gaussian medium (σ=0.10) | 26.33% | −3.88 |
| Gaussian wide (σ=0.20) | 26.86% | −3.35 |

Cosine smooth transition performs equivalently to the hard mask, confirming no pathological ringing artifacts. Gaussian masks with wider transition bands degrade performance because the soft rolloff partially exposes HF components to spectral gating, reintroducing the attenuation mechanism diagnosed in Section 3.2.

---

## Table 3: Model Efficiency

| Model | Params (M) | Throughput (img/s) |
|-------|-----------|-------------------|
| SpectFormer | 19.66 | 2364.8 |
| SpectFormer + HF-Skip | 19.66 | 2901.1 |
| SpectFormer + ReInit (identity) | 19.66 | 2946.7 |
| GFNet + HF-Skip | 15.62 | 3563.9 |

HF-Skip adds zero parameters and is faster than baseline SpectFormer (+23% throughput) because restricting gating to the LF band reduces complex multiplications.

---

## Table 4: Initialization Ablation

| Init Mode | DTD Test | ImageNet-100 Val |
|-----------|---------|-----------------|
| Original (random × 0.02) | 22.98% | 82.45% |
| Identity (mag=1, phase=0) | 27.77% | 83.46% |
| Unit magnitude | 27.02% | 83.28% |
| HF-Skip (multi-seed mean) | 30.27% | 83.76% |

### Gap Analysis

| Metric | DTD | ImageNet-100 |
|--------|-----|-------------|
| Identity closes | 73% of gap (4.79/6.60) | 79% of gap (1.01/1.27) |
| HF-Skip still wins by | +2.50% | +0.30% |

Better initialization closes 73–79% of the gap, but HF-Skip still outperforms on both datasets. Optimization re-introduces HF suppression even from favorable initializations.

---

## Table 5: Dense Prediction — Segmentation Results

### Kvasir-SEG (Medical Polyp Segmentation)

| Model | Encoder Params | Total Params | Dice | IoU |
|-------|---------------|-------------|------|-----|
| GFNet | 16.87M | ~18.1M | 0.6801 | 0.5589 |
| SpectFormer | 20.92M | ~22.1M | 0.7225 | 0.6186 |
| HF-Skip | 20.92M | ~22.1M | 0.7385 | 0.6256 |
| ViT | 22.94M | ~24.1M | 0.7526 | 0.6428 |

### VOC 2012 Segmentation

| Model | Params | mIoU | Pixel Acc | Δ mIoU vs SpectFormer |
|-------|--------|------|-----------|----------------------|
| GFNet | ~18.1M | 0.0998 | 0.7345 | −1.94% |
| SpectFormer | ~22.1M | 0.1192 | 0.7458 | — |
| ViT | ~24.1M | 0.1355 | 0.7530 | +1.63% |
| HF-Skip | ~22.1M | 0.1421 | 0.7605 | +2.29% |

On VOC 2012, HF-Skip surpasses all models including ViT. On Kvasir-SEG, HF-Skip improves over SpectFormer and closes the gap to ViT, which does not suffer from HF collapse as it lacks spectral gating.

---

## Table 6: Cross-Architecture Validation — GFNet + HF-Skip

| Model | ImageNet-100 Top-1 | Δ vs baseline |
|-------|-------------------|---------------|
| GFNet (baseline) | 81.56% | — |
| GFNet + HF-Skip | 84.02% | +2.46 |
| SpectFormer (baseline, multi-seed mean) | 82.49% | — |
| HF-SpectFormer (multi-seed mean) | 83.76% | +1.27 |

HF-Skip generalizes beyond SpectFormer. GFNet benefits even more (+2.46 vs +1.27) because it lacks downstream attention layers to compensate for HF loss.

---

## Table 7: Robustness — 10 Corruption Types (ImageNet-100)

### Gaussian Noise (std dev σ)

| σ | SpectFormer | HF-Skip | ViT | GFNet | GFNet+HFSkip |
|---|------------|---------|-----|-------|-------------|
| 0.08 | 75.1% | **77.0%** | 69.7% | 74.9% | 76.8% |
| 0.12 | 71.2% | **73.1%** | 64.9% | 70.8% | 73.5% |
| 0.18 | 63.9% | **65.1%** | 55.3% | 64.7% | 66.7% |
| 0.26 | 51.2% | 50.2% | 40.5% | **53.0%** | 51.0% |
| 0.38 | 30.2% | 25.9% | 19.5% | **33.0%** | 26.1% |

### Shot Noise (Poisson λ, lower = more noise)

| λ | SpectFormer | HF-Skip | ViT | GFNet | GFNet+HFSkip |
|---|------------|---------|-----|-------|-------------|
| 60 | 75.4% | 76.3% | 69.9% | 74.4% | **77.3%** |
| 25 | 71.0% | 71.7% | 63.4% | 69.8% | **73.1%** |
| 12 | 62.6% | 64.3% | 53.6% | 64.3% | **65.7%** |
| 5 | 46.8% | 44.1% | 35.0% | **50.4%** | 45.6% |
| 3 | 33.3% | 27.9% | 22.4% | **36.3%** | 29.5% |

### Impulse Noise (salt-and-pepper probability p)

| p | SpectFormer | HF-Skip | ViT | GFNet | GFNet+HFSkip |
|---|------------|---------|-----|-------|-------------|
| 0.03 | 70.6% | 71.0% | 64.3% | 70.9% | **72.0%** |
| 0.06 | 62.0% | 62.9% | 55.3% | **65.5%** | 65.3% |
| 0.09 | 55.7% | 55.8% | 47.4% | **60.1%** | 59.3% |
| 0.17 | 40.3% | 40.1% | 33.9% | **47.4%** | 46.6% |
| 0.27 | 26.7% | 23.8% | 23.0% | **33.6%** | 32.4% |

### Defocus Blur (Gaussian radius r pixels)

| r | SpectFormer | HF-Skip | ViT | GFNet | GFNet+HFSkip |
|---|------------|---------|-----|-------|-------------|
| 3 | 47.6% | 46.3% | 41.1% | **50.2%** | 47.3% |
| 4 | 35.6% | 34.1% | 30.6% | **37.1%** | 33.1% |
| 6 | **21.5%** | 21.0% | 18.7% | **21.5%** | 17.3% |
| 8 | 14.9% | **15.3%** | 13.0% | 14.0% | 10.9% |
| 10 | 10.9% | **11.2%** | 9.8% | 9.6% | 7.5% |

### Motion Blur (Gaussian radius r pixels)

| r | SpectFormer | HF-Skip | ViT | GFNet | GFNet+HFSkip |
|---|------------|---------|-----|-------|-------------|
| 3 | **47.6%** | 46.3% | 41.1% | 50.2% | 47.3% |
| 5 | **27.7%** | 26.2% | 23.3% | 27.6% | 23.5% |
| 7 | **21.5%** | 21.0% | 18.7% | **21.5%** | 17.3% |
| 8 | 14.9% | **15.3%** | 13.0% | 14.0% | 10.9% |
| 10 | **10.9%** | 11.2% | 9.8% | 9.6% | 7.5% |

### Zoom Blur (zoom factor z)

| z | SpectFormer | HF-Skip | ViT | GFNet | GFNet+HFSkip |
|---|------------|---------|-----|-------|-------------|
| 1.11 | **69.8%** | 68.3% | 67.6% | 68.0% | 69.2% |
| 1.16 | **60.8%** | 58.1% | 58.9% | 59.6% | 60.2% |
| 1.21 | **56.6%** | 53.5% | 55.6% | 55.7% | 54.9% |
| 1.26 | **50.6%** | 46.1% | 49.9% | 50.5% | 48.0% |
| 1.31 | **45.0%** | 39.9% | 44.6% | 44.7% | 42.0% |

### Snow

| Parameters | SpectFormer | HF-Skip | ViT | GFNet | GFNet+HFSkip |
|-----------|------------|---------|-----|-------|-------------|
| μ=0.1, σ=0.3, α=0.5 | 69.2% | 70.2% | 64.2% | 70.6% | **71.7%** |
| μ=0.2, σ=0.3, α=0.5 | 66.7% | 68.0% | 61.7% | 68.7% | **70.0%** |
| μ=0.55, σ=0.3, α=0.9 | 35.1% | 33.1% | 28.0% | **41.0%** | 39.0% |
| μ=0.55, σ=0.3, α=0.85 | 38.4% | 36.8% | 30.8% | **43.5%** | 42.4% |
| μ=0.55, σ=0.3, α=0.85 | 39.1% | 37.0% | 31.4% | **44.2%** | 42.3% |

### Frost (image blend α, frost blend β)

| Parameters | SpectFormer | HF-Skip | ViT | GFNet | GFNet+HFSkip |
|-----------|------------|---------|-----|-------|-------------|
| α=1.0, β=0.4 | 75.8% | 78.3% | 73.0% | 75.7% | **78.8%** |
| α=0.8, β=0.6 | 74.3% | 76.7% | 70.5% | 73.5% | **77.1%** |
| α=0.7, β=0.7 | 73.2% | 75.9% | 68.9% | 72.5% | **76.0%** |
| α=0.65, β=0.7 | 73.8% | 76.5% | 69.8% | 72.9% | **76.7%** |
| α=0.6, β=0.75 | 73.0% | 76.3% | 69.0% | 72.2% | **75.8%** |

### Brightness (offset Δ)

| Δ | SpectFormer | HF-Skip | ViT | GFNet | GFNet+HFSkip |
|---|------------|---------|-----|-------|-------------|
| +0.1 | 79.2% | 80.2% | 76.1% | 77.8% | **80.8%** |
| +0.2 | 78.0% | 79.8% | 75.2% | 77.1% | **80.3%** |
| +0.3 | 76.7% | 78.8% | 73.8% | 76.1% | **79.4%** |
| +0.4 | 74.7% | 77.0% | 70.9% | 74.0% | **77.6%** |
| +0.5 | 71.8% | 74.3% | 68.3% | 71.5% | **74.4%** |

### Contrast (scale factor c, lower = less contrast)

| c | SpectFormer | HF-Skip | ViT | GFNet | GFNet+HFSkip |
|---|------------|---------|-----|-------|-------------|
| 0.40 | 75.6% | 77.2% | 71.9% | 74.6% | **78.0%** |
| 0.30 | 74.1% | 76.0% | 70.0% | 73.4% | **76.1%** |
| 0.20 | 70.3% | 72.1% | 66.6% | 71.2% | **73.9%** |
| 0.10 | 56.9% | 59.8% | 54.5% | 63.3% | **67.5%** |
| 0.05 | 35.5% | 38.8% | 32.8% | 44.0% | **50.6%** |

### Summary

HF-Skip improves robustness for corruptions that preserve image structure (noise at mild-moderate levels, frost, brightness, contrast). Under blur corruptions, which destroy HF content by design, SpectFormer's inherent HF suppression becomes accidentally beneficial. Under extreme noise (where all models degrade to 25–35%), SpectFormer's suppression acts as implicit denoising. This pattern is consistent with our thesis: HF-Skip's benefit is proportional to the signal value of the HF content present.

---

## Figure 1: HF Collapse Across Scales (Untrained Models)

![Scale Analysis](scale_spectral_block_comparison.png)

Per-block LF/HF ratios for untrained SpectFormer (top) and HF-SpectFormer (bottom) at Small, Base, and Large scales across three input types. HF collapse persists and worsens at larger scales for SpectFormer. HF-SpectFormer maintains consistent HF preservation across all scales.

---

## Figure 2: Feature Map Visualizations

![Feature Maps](feature_maps.png)

Feature maps at Block 0 output for different mask types on a DTD texture image. The hard mask and cosine mask show preserved fine structure with no ringing artifacts. Gaussian masks show visible loss of detail due to partial HF exposure to spectral gating.

---

## Figure 3: Mask Shapes

![Mask Shapes](mask_shapes.png)

LF mask shapes for all five variants. Green = gated (LF), Red = skipped (HF). The Gaussian masks create wide transition zones where mid-frequency components receive partial exposure to both pathways.
