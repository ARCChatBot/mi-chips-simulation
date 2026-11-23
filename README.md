# MI-CHIPS Student Success & Gate Simulation

An interactive, single-page simulation of the **MI-CHIPS** (Mixed Immersion Cleanroom & Hardware Intelligence Progression System) certification model: a gated logistic regression that estimates a student's probability of cleanroom certification from five training layers and total training time.

Open `index.html` in any browser. It has no build step and no dependencies.

## What it shows

- **Five training layers** with sliders: CBT Theory → 3D Simulation → VR Cleanroom → AR Data Viz → Physical Mastery.
- **75% prerequisite gate**: layers unlock in order. The first score below 75% blocks that layer, and every later layer contributes nothing.
- **Progression pipeline**: an animation showing how far the student gets and where they are blocked.
- **Sigmoid plot**: the logistic curve with the current log-odds `z` and the tier thresholds marked.
- **Breakdown of z**: how much the intercept, each layer, the time penalty and the learning-velocity boost add to `z`.
- **Tier & action**: Green / Yellow / Red, with the matching automated action.
- **Shareable scenarios**: every slider setting is saved in the URL, so you can send a link to any scenario.

## Model (v2.1)

```
C   = complexity weight of the highest layer cleared
P̄   = mean score of the cleared layers
LV  = (C · P̄) / (100 · T_seconds)

z   = −5.17 + Σ wᵢ · sᵢ   (cleared layers only)
          − 0.00015 · T_seconds
          + 3200 · LV

p̂   = 1 / (1 + e^(−z))
```

| Layer | Complexity Cᵢ | Score weight wᵢ |
|---|---|---|
| 01 CBT Theory | 1.0 | 0.012 |
| 02 3D Simulation | 2.5 | 0.015 |
| 03 VR Cleanroom | 4.0 | 0.022 |
| 04 AR Data Viz | 5.5 | 0.016 |
| 05 Physical Mastery | 8.0 | 0.021 |

**Tiers:** blocked at any gate → Red (gate blocked). Otherwise p̂ ≥ 85% → Green, 50–85% → Yellow, under 50% → Red (high risk).

All coefficients are defined in one `MODEL` object near the top of the script in `index.html`.

## Presets

| Preset | Scores (L1–L5) | Time | Result |
|---|---|---|---|
| Passing | 80 / 75 / 80 / 80 / 80 | 130.35 min | Green, p̂ ≈ 95.5% |
| Advisory | 78 / 76 / 77 / 75 / 76 | 240 min | Yellow, p̂ ≈ 64.4% |
| At-Risk | 80 / 62 / 0 / 0 / 0 | 208.33 min | Red (gated at Layer 02) |
