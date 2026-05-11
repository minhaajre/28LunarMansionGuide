# CLAUDE.md — 28 Lunar Mansions

Project instructions for Claude Code. Read before touching any file.

---

## Project Identity

**What it is:** A client-facing reference tool for Abu Ma'shar's 28 Lunar Mansions (Manazil al-Qamar), mapping Gregorian calendar dates to the moon's mansion position. Used directly by paying clients of CCIAF.

**Factual accuracy is the primary constraint.** Any date-to-mansion mapping error is a client-trust failure. Do not change the astronomical algorithm without cross-validating against Stellarium or AstroSeek for at least 3 independent dates.

**Owner:** Minhaaj Rehman (CCIAF)  
**Deploy target:** Vercel (auto-deploys on push to `main`)  
**Stack:** Vanilla HTML/CSS/JS — no build step, no framework.

---

## Architecture

```
index.html                  ← internal/practitioner view
28mansionsclientfacing.html ← client-facing (simpler, same algorithm)
vercel.json                 ← cache headers + cleanUrls config
docs/
  algorithm.md              ← astronomical formula documentation
  sop-deploy.md             ← deploy checklist
```

Both HTML files share identical JavaScript. If you fix the algorithm in one, apply the same fix to the other.

---

## The Algorithm

Moon longitude is computed via a simplified Meeus series (8 perturbation terms). Accuracy: ±1–2°. This is adequate — each mansion spans 12.857° (360°/28).

Key functions (identical in both HTML files):
- `toJD(year, month, day, hour)` — Gregorian → Julian Day Number
- `moonLon(jd)` — JD → tropical ecliptic longitude (0°–360°)
- `lonToMansion(lon)` — longitude → mansion number (1–28), equal 12.857° slices from 0° Aries
- `mansionDatesForMonth(year, month)` — samples every 2h across the month, assigns days to mansions

**Known precision limit:** The simplified formula has ±2° error, growing slightly over decades past J2000. Not a correctness issue for mansion assignment but worth noting for edge-day transitions.

**Validated dates:**
- Jun 27, 2027 → M1 Al-Sharatain (Aries 8.9°) ✓
- Jun 26, 2026 → M19 Al-Shaulah (Scorpio 26.6°) ✓

---

## SOPs

### Before editing the algorithm
1. Identify the exact date and mansion the user says is wrong.
2. Run the formula manually in Node: `node -e "..."` to get the computed longitude.
3. Cross-check against AstroSeek or Stellarium for that date.
4. Only edit if the formula and the reference disagree by more than 2°.
5. After editing, validate at least 3 non-adjacent dates before committing.

### Before editing mansion data (MANSIONS array)
1. Check Abu Ma'shar's source via the Ibn Arabi / Arabic tradition — degree boundaries are fixed.
2. Do not reorder or renumber mansions.
3. Both HTML files must be updated identically.

### Prohibited without explicit approval
- Switching from tropical to sidereal zodiac (changes the entire system).
- Adding external API dependencies (client-facing tools must not have runtime API calls).
- Splitting into multiple JS files (Vercel serves static HTML — no bundler).
- Changing mansion boundaries to unequal spacing without sourcing Abu Ma'shar directly.

---

## Deploy Flow

```
branch → edit → commit → push → Vercel preview URL → verify date mappings → merge to main
```

Vercel auto-deploys `main`. No CLI deploy needed. See `docs/sop-deploy.md` for the full checklist.

---

## Style Conventions

- JS: vanilla ES6, no imports, no build.
- CSS: custom properties only (`--var` tokens), no utility frameworks.
- HTML: single-file. Keep it single-file.
- Commits: imperative, lowercase (`fix moon longitude for edge-day transitions`).
- No comments in code except for non-obvious astronomical constants.
