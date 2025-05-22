# Lesson 13: Calibration and Quality Control

Sensors drift; trusting un‑calibrated data is a recipe for error.

---

### Calibration hierarchy
1. **Primary standard** (traceable reference, e.g. buffer solution)  
2. **Field calibration** (adjust sensor to match)  
3. **Post‑deployment correction** (back‑adjust data using lab check)

### Good practice
* Three‑point calibration for pH: 4, 7, 10.  
* Zero + span for turbidity: DI water & 100 NTU standard.  
* DO: 100 % saturation in air‑water bath + zero in sodium sulfite.

### QC flags (delta‑check)
| Flag | Condition |
|------|-----------|
| “Spike” | |valueₙ – valueₙ₋₁| > 3 σ |
| “Drift” | 7‑day mean deviates > 10 % from baseline |
| “Flat‑line” | Std dev < threshold for 6 h |

Automated scripts (e.g. R `waterData` package) apply flags; analysts review before publishing.

---

**Quick check**  
Why is a two‑point calibration inadequate for a logarithmic sensor response like pH?

