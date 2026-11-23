# MI-CHIPS Student Success Logistic Regression Model

An interactive, single-page simulation of the **MI-CHIPS** (Mixed Immersion Cleanroom & Hardware Intelligence Progression System) certification model: a gated logistic regression that estimates a student's probability of cleanroom certification from the score and time of each of five training layers.

Open `index.html` in any browser. It has no build step and no dependencies.

## What it shows

- **Student scores**: each of the five training layers (CBT Theory → 3D Simulation → VR Cleanroom → AR Data Viz → Physical Mastery) has a score slider and a time slider in seconds. The total time T is the sum of the layer times.
- **Result**: the risk tier and the chance of certification, with a one-line explanation and the next step.
- **Gate progress**: which layers are passed, which one is blocked, and which are locked. Layers unlock in order, and the first score below 74.5% blocks everything after it.
- **Show the math · 3 steps** (collapsed by default):
  1. check the gates and compute each layer's own learning velocity, then average them;
  2. add up the evidence into the log-odds `z`;
  3. convert `z` into a probability with the sigmoid.

  Each step has a plain-language explanation, its formula, a live table, chart or values, and a sentence describing the current student.
- **Shareable scenarios**: every score and time is saved in the URL.

Styled with the Operation SEMI / MI-CHIPS presentation color system (C01–C12) and the Inter typeface.

## Model

```
Gate      layer i counts only if layers 1…i all scored ≥ 74.5

LVᵢ  = (Cᵢ · Pᵢ) / (100 · Tᵢ)        each passed layer, own complexity, proficiency, seconds
LV̄   = average of LVᵢ over passed layers
T    = Σ Tᵢ                            total seconds across all five layers

z    = −5.17 + Σ wᵢ · Pᵢ   (passed layers only)
           − 0.00015 · T
           + 677.6 · LV̄

p̂    = 1 / (1 + e^(−z))
```

| Layer | Complexity Cᵢ | Score weight wᵢ |
|---|---|---|
| 01 CBT Theory | 1.0 | 0.012 |
| 02 3D Simulation | 2.5 | 0.015 |
| 03 VR Cleanroom | 4.0 | 0.022 |
| 04 AR Data Viz | 5.5 | 0.016 |
| 05 Physical Mastery | 8.0 | 0.021 |

**Tiers:** blocked at any gate → Red (gate blocked). Otherwise p̂ ≥ 85% → Green, 50–85% → Yellow, under 50% → Red (high risk).

**About the 677.6 coefficient.** Model v2.1 used `3200 · LV` with a single combined velocity, LV = C·P/(100·T), where C is the highest complexity passed and P the average score. The per-layer velocities are larger, so the coefficient was rescaled so that the reference record in the MI-CHIPS Systems Document keeps the same z: 3200 × 0.000805 / 0.003801 ≈ 677.6. It is a rescaling, not a refit on cohort data.

The pass mark (74.5) follows the MI-CHIPS Systems Document. All coefficients are defined in one `MODEL` object near the top of the script in `index.html`.

## Presets

| Preset | Scores (L1–L5) | Times in seconds (L1–L5) | Result |
|---|---|---|---|
| Passing (reference record) | 80 / 74.5 / 80 / 80 / 78.9 | 1424.73 / 1763.56 / 649.65 / 411.51 / 3571.76 | Green, z ≈ +3.01, p̂ ≈ 95.3% |
| Borderline | 78 / 76 / 77 / 75 / 76 | 2400 / 3000 / 1500 / 1200 / 4800 | Yellow, p̂ ≈ 62.4% |
| Blocked | 80 / 62 / 0 / 0 / 0 | 4500 / 8000 / 0 / 0 / 0 | Red (gated at Layer 02) |
