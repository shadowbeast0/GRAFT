# Ablation and sensitivity studies

Every variant in a table shares the
same folds/splits, seeds and Stage-1 (visual baseline) model, so the rows of one table are directly
comparable.

Hyperparameter symbols follow the paper: α (centroid anchoring, Eq. 1), λ (prior weight in
p_joint, Eq. 2), m (candidate classes kept by PGSM), γ (boundary distance decay), β (homophily
weight in A_total), ω (visual weight in z_final), K (learned regions), k_sp (spatial neighbours).

---

## 1. UBC-OCEAN — component ablation

5-fold stratified cross-validation, balanced accuracy (%), mean over folds.

| Variant | BAcc |
|---|---|
| **GRAFT (full) — run reported in the paper (Table 1)** | **67.56** |
| Visual baseline — matched ablation run | 67.39 |
| w/o zero-centering | 64.74 |
| w/o PGSM | 65.29 |
| w/o CBR | 66.21 |
| w/o UGKF | 66.59 |


## 2. UBC-OCEAN — sensitivity to the mixing weights

5-fold CV, balanced accuracy (%). Paper values λ = 0.70, β = 0.65. Setting a weight to 1.0 disables
the second term. All rows share the same Stage-1 model per fold.

| Setting | BAcc |
|---|---|
| Visual baseline (Stage 1) | 66.63 |
| **Paper setting (λ = 0.70, β = 0.65)** | **67.56** |
| λ = 0.50 | 64.87 |
| λ = 0.90 | 65.27 |
| λ = 1.00 (no semantic term) | 64.82 |
| β = 0.50 | 65.01 |
| β = 0.80 | 64.12 |
| β = 1.00 (no boundary edges) | 64.88 |

---

## 3. Osteosarcoma — component ablation

Official split, test accuracy (%) on n = 177 images, mean over 6 seeds
(3407, 42, 123, 7, 2024, 31337).

| Variant | Acc |
|---|---|
| Visual baseline | 94.26 |
| Baseline trained 30 more epochs (no knowledge) | 93.41 |
| GRAFT + all paper components (zero-centering, α, PGSM, UGKF, ω-fusion) | 94.92 |
| ↳ w/o zero-centering | 93.60 |
| ↳ w/o centroid anchoring (α = 0) | 93.31 |
| ↳ w/o PGSM | 93.60 |
| ↳ w/o UGKF uncertainty gate | 93.03 |
| ↳ w/o visual fusion (ω) | 93.31 |
| ↳ w/o semantic (CBR) edges | 93.88 |
| ↳ w/o cross-attention | 93.97 |]
| ↳ wrong-organ (lymphoma) ontology | 93.03 |

## 4. Osteosarcoma — sensitivity of fixed constants

Official split, test accuracy (%), mean over 3 seeds (3407, 42, 123). Default values in bold.

| Constant | Value | Acc |
|---|---|---|
| Retrieved rules per image | 1 | 93.60 |
| | 2 | 94.35 |
| | **4** | **94.92** |
| | 8 | 94.54 |
| | 12 | 94.16 |
| Retrieval temperature | 0.03 | 94.35 |
| | **0.1** | **94.92** |
| | 0.3 | 94.54 |
| | 1.0 | 93.79 |
| Semantic neighbours | 2 | 94.16 |
| | **4** | **94.92** |
| | 8 | 94.35 |
| Spatial neighbours k_sp | 4 | 93.97 |
| | **8** | **94.92** |
| | 12 | 94.54 |
| Text-alignment loss weight | 0.1 | 93.97 |
| | 0.25 | 94.54 |
| | **0.5** | **94.92** |
| | 1.0 | 94.35 |
| Visual token dropout | 0.0 | 94.35 |
| | **0.1** | **94.92** |
| | 0.3 | 93.79 |
| Learned regions K | 25 | 94.16 |
| | **49** | **94.92** |

| Other variants (same 3 seeds) | Acc |
|---|---|
| Visual baseline (K = 49) | 93.41 |
| Visual baseline (K = 25) | 93.03 |
| No text-alignment loss | 93.79 |
| Fixed gate | 94.16 |
| No gate regulariser | 94.35 |
| No warm start | 93.97 |
