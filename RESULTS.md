# Detailed results

Supplementary tables for the paper's results section.

## UBC-OCEAN — pooled 5-fold confusion matrices (N = 513)

| Truth | n | Baseline: CC | EC | HGSC | LGSC | MC | GRAFT: CC | EC | HGSC | LGSC | MC |
|---|---|---|---|---|---|---|---|---|---|---|---|
| CC | 94 | **70** | 8 | 14 | 0 | 2 | **70** | 7 | 16 | 0 | 1 |
| EC | 119 | 10 | **71** | 30 | 4 | 4 | 11 | **78** | 22 | 4 | 4 |
| HGSC | 217 | 11 | 18 | **176** | 9 | 3 | 16 | 21 | **168** | 9 | 3 |
| LGSC | 42 | 2 | 4 | 13 | **23** | 0 | 2 | 6 | 13 | **21** | 0 |
| MC | 41 | 2 | 7 | 6 | 0 | **26** | 2 | 6 | 3 | 1 | **29** |

## MIDOG 2025 Track 2 — test-split confusion matrices (N = 2,543)

| Truth | n | Baseline: AMF | NMF | GRAFT: AMF | NMF |
|---|---|---|---|---|---|
| AMF | 340 | **304** | 36 | **310** | 30 |
| NMF | 2203 | 197 | **2006** | 195 | **2008** |

## Clinical reasoning and gate telemetry audit

Moved to [CLINICAL_RETRIEVAL_AUDIT.md](CLINICAL_RETRIEVAL_AUDIT.md), which shows each class's retrieved hallmark next to an example image.
