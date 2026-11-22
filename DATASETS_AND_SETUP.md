# Datasets and experimental setup

## Dataset links

| Dataset | Official source | Access / licence |
|---|---|---|
| UBC-OCEAN | [Kaggle competition page](https://www.kaggle.com/competitions/UBC-OCEAN) | Kaggle account; accept the competition rules |
| TCIA Osteosarcoma | [TCIA collection page](https://www.cancerimagingarchive.net/collection/osteosarcoma-tumor-assessment/) · DOI [10.7937/tcia.2019.bvhjhdas](https://doi.org/10.7937/tcia.2019.bvhjhdas) | Open; TCIA Data Usage Policy |
| MIDOG 2025, Track 2 | [Challenge datasets page](https://midog2025.deepmicroscopy.org/datasets) · Zenodo DOI [10.5281/zenodo.15188326](https://doi.org/10.5281/zenodo.15188326) | Open; CC BY-NC 4.0 |

## Datasets

### UBC-OCEAN (ovarian carcinoma subtyping)
- **Source:** Kaggle *UBC Ovarian Cancer Subtype Classification and Outlier Detection* (UBC-OCEAN),
  <https://www.kaggle.com/competitions/UBC-OCEAN>.
- **Images used:** the 513 whole-slide images (tissue micro-arrays are excluded), represented by
  the slide **thumbnails** supplied with the dataset, not the gigapixel scans.
- **Classes (n):** Clear cell CC (94), Endometrioid EC (119), High-grade serous HGSC (217),
  Low-grade serous LGSC (42), Mucinous MC (41); imbalance 5.3 : 1.
- **Protocol:** 5-fold stratified cross-validation (80 : 20 train : test per fold); metric = balanced accuracy (macro-F1 also reported).
- **Tiling:** each thumbnail is cut into a 4 × 5 grid of 512-px tiles, resized to 224 × 224.
  Training samples 16 tiles per slide (75 % chosen by highest nuclear density, 25 % at random,
  ±48 px positional jitter, flips); evaluation uses all 20 tiles.

### TCIA Osteosarcoma (treatment-response assessment)
- **Source:** TCIA *Osteosarcoma data from UT Southwestern / UT Dallas for Viable and Necrotic
  Tumor Assessment* (H&E tiles), <https://www.cancerimagingarchive.net/collection/osteosarcoma-tumor-assessment/>
  (data citation: Leavey et al., 2019, DOI 10.7937/tcia.2019.bvhjhdas).
- **Classes:** Non-tumor, Non-viable (necrotic) tumor, Viable tumor.
- **Split used:** fixed train / validation / test split, 61 : 22 : 17 — train 640 (340 / 144 / 156),
  validation 231 (88 / 66 / 77), test 177 (91 / 42 / 44).
- **Metric:** test accuracy (one test image = 0.56 points).

### MIDOG 2025, Track 2 (atypical vs. normal mitotic figures)
- **Source:** MIDOG 2025 challenge, Track 2 (atypical mitotic figure classification): *MIDOG 2025 Atypical
  Training Set*, <https://midog2025.deepmicroscopy.org/datasets>
  (Zenodo: <https://doi.org/10.5281/zenodo.15188326>).
- **Images:** 11,935 mitotic-figure crops of 128 × 128 px, labelled atypical (AMF, 14.6 %) or
  normal (NMF) by three-expert majority vote.
- **Split:** slide-grouped train 7,477 / validation 1,915 / test 2,543 figures (63 : 16 : 21), split by slide.
- **Metric:** balanced accuracy (macro-F1 also reported).

## Backbones
| Dataset | Backbone | Patch tokens |
|---|---|---|
| UBC-OCEAN | UNI ViT-L/16 (last 3 blocks fine-tuned) | 14 × 14 = 196, 1024-d |
| Osteosarcoma | PLIP ViT-B/32 | 7 × 7 = 49 |
| MIDOG 2025 | UNI ViT-L/16 | 14 × 14 = 196 |

## Training
- **Stage 1 – visual baseline:** backbone + segmentation head + spatial graph (+ dual-pooling ABMIL
  for UBC-OCEAN), trained with Focal Loss (γ_focal = 2) and AdamW with layer-wise learning rates
  (UBC-OCEAN: backbone 8e-6, new layers 1e-4; 35 epochs).
- **Stage 2 – knowledge modules:** PGSM retrieval, CBR and UGKF trained with
  classification, text-alignment, triplet and gate-calibration losses; the fusion head is
  warm-started from the Stage-1 classifier (UBC-OCEAN: 30 epochs, 30 % tile dropout;
  Osteosarcoma: 25 + 30 epochs).
- **Input & augmentation:** 224 × 224, flips and colour jitter.
- **Hardware / software:** PyTorch, NVIDIA T4 GPUs (Kaggle).

## Hyperparameters
| Symbol | Meaning | Value |
|---|---|---|
| K | learned tissue regions per image | 49 |
| k_sp | spatial neighbours per region | 6 (8 for GRAFT on Osteosarcoma) |
| α | centroid anchoring weight in ẽ_j (Eq. 1) | 0.55 |
| λ | weight of the visual prior in p_joint (Eq. 2) | 0.70 |
| m | candidate classes kept by PGSM | 2 |
| γ | distance decay of boundary edges in A_diff | 2.0 |
| β | weight of homophilic edges in A_total | 0.65 |
| ω | weight of the visual logits in z_final (Eq. 3) | 0.45 |
| – | rules retrieved per image | 4 |
| – | retrieval softmax temperature | 0.1 |

Sensitivity of these values is reported in [ABLATIONS.md](ABLATIONS.md).
