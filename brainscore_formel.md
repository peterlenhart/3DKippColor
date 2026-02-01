# Brain Score – Final Formula (locked)

This document defines the final Brain Score calculation.
The formula is tuned to match human intuition and fixed anchor points.

---

## Fixed Anchor Points

- Observation Time = 15 s, Hits = 6  → Brain Score = 100 %
- Observation Time = 75 s, Hits = 3  → Brain Score = 69 %
- Observation Time = 150 s, Hits = 1 → Brain Score = 12 %
- Hits = 0 → No percentage value
  Display text: "No Hit, no Score"

---

## Definitions

- T = total observation time in seconds (including all joker time)
- H = number of hits (0…6)
- Maximum observation time: 150 s
- Free observation time: 15 s
- Extra observation time range: 135 s

Clamp:
- If T < 15 → T = 15
- If T > 150 → T = 150

---

## Time Factor

Extra observation time:

E = clamp(T - 15, 0…135)

Time factor:

S_time = 1 - 0.4 * (E / 135) ^ 0.855

Result range:
- S_time = 1.00 at T = 15 s
- S_time = 0.60 at T = 150 s

---

## Hit Factor (logistic, normalized)

S_hits = [1 / (1 + exp(-1.611 * (H - 1.860)))]
         / [1 / (1 + exp(-1.611 * (6 - 1.860)))]

Special rules:
- If H = 0 → no Brain Score percentage
- Display instead: "No Hit, no Score"

---

## Final Brain Score

BrainScore (%) = 100 * S_time * S_hits

---

## Display Rules

- H = 0:
  "Brain Score: No Hit, no Score"

- H = 1 and T = 150 s:
  Approx. 12 %
  Optional label: "One-Hit Lucky-Kip"

- H ≥ 1:
  Display Brain Score in %

---

## Design Intent

- Hits dominate over time
- Extra observation time reduces score smoothly
- 6 hits are always strong, even with long observation time
- Random luck is capped and clearly separated from skill

This formula is final and must not be modified without re-validating all anchor points.
