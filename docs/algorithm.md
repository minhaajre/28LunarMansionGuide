# Algorithm Documentation

## Overview

Moon longitude is computed client-side in vanilla JavaScript using a simplified truncation of Meeus' *Astronomical Algorithms* (2nd ed., Chapter 47). No external API is called at runtime.

## Formula Chain

```
Gregorian date → Julian Day Number → Moon longitude (tropical) → Mansion number
```

### 1. `toJD(year, month, day, hour)` — Gregorian to JD

Standard Julian Day Number formula (valid for all Gregorian dates post-1582). Validated: returns 2,451,545.0 for Jan 1.5, 2000 (J2000 epoch).

### 2. `moonLon(jd)` — Moon tropical longitude

Computes 8 perturbation terms against J2000 epoch:

| Term | Coefficient | Argument |
|------|-------------|----------|
| Mean longitude L | — | base |
| Evection | +6.289° | M (Moon anomaly) |
| Variation | +1.274° | 2D − M |
| Annual equation | +0.658° | 2D |
| Reduction to ellipse | +0.214° | 2M |
| Solar equation | −0.186° | Ms (Sun anomaly) |
| Inclination | −0.114° | 2F |
| Parallactic inequality | +0.059° | 2D − 2M |
| Combined | +0.057° | 2D − Ms − M |

**Accuracy:** ±1–2°. Full ELP2000 has ~hundreds of terms; this truncation is sufficient for mansion assignment since each mansion spans **12.857°** (360°/28).

### 3. `lonToMansion(lon)`

```js
(Math.floor(lon / (360/28)) % 28) + 1
```

Divides the 360° ecliptic into 28 equal segments of 12°51'26" starting at 0° tropical Aries. This is Abu Ma'shar's tropical framework.

### 4. `mansionDatesForMonth(year, month)`

Samples every 2 hours across the month (12 samples/day). If any sample in a day resolves to mansion N, that day is included in N's date set. This handles overnight mansion transitions correctly.

## Validated Reference Dates

Cross-checked against AstroSeek and manual Meeus calculation:

| Date | Computed lon | Mansion | Sign |
|------|-------------|---------|------|
| Jun 26, 2026 | 236.6° | M19 Al-Shaulah | Scorpio 26.6° |
| Jun 27, 2026 | 248.5° | M20 Al-Na'aim | Sagittarius 8.5° |
| Jun 26, 2027 | 356.5° | M28 Al-Batn al-Hut | Pisces 26.5° |
| Jun 27, 2027 | 8.9° | M1 Al-Sharatain | Aries 8.9° |

## Known Limitations

- **Sidereal vs. tropical:** This uses the tropical zodiac (vernal equinox = 0° Aries). Abu Ma'shar explicitly used tropical. Do not switch to sidereal without full re-validation.
- **Timezone:** JD computed at UTC noon for each day. Browser local time is not used in the calculation. Users in UTC+7 will see dates that correspond to UTC noon positions.
- **Long-range accuracy:** The simplified Meeus series accumulates small errors decades away from J2000 (2000). For dates 2024–2030 the error is within ±2°.
