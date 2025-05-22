# Lesson 11: Integrating Parameters — The Water Quality Index

A Water Quality Index (WQI) rolls multiple parameters into a single score (0–100).

---

### Simple WQI formula
For each parameter i, compute quality rating qi (% of guideline).  
Weight by wi (importance).  
WQI = Σ(qi · wi) ⁄ Σwi.

### Example weights
| Parameter | wi |
|-----------|----|
| Dissolved O₂ | 4 |
| pH | 3 |
| Conductivity | 2 |
| Nitrate | 4 |
| Turbidity | 3 |

Plotting WQI daily highlights overall river health and helps non‑specialists see trends.

### Sensor link
Continuous data feed WQI in real time. An overnight DO crash drags WQI down instantly; an alarm is triggered in the control room.

---

**Try this**  
Design your own mini‑index using three parameters you care about. Justify the weightings.

