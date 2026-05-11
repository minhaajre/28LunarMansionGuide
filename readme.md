# 28 Lunar Mansions — CCIAF Reference Tool

A client-facing Gregorian-to-mansion calendar based on Abu Ma'shar's 28 Lunar Mansions (Manazil al-Qamar). Computes the moon's tropical longitude for each day of a selected month and assigns it to one of the 28 equal mansions.

**Live:** [cciaf.minhaaj.com](https://cciaf.minhaaj.com)

---

## What It Does

- Displays all 28 mansions with Abu Ma'shar degree ranges, Arabic letters, divine names, and electional ratings.
- Shows which Gregorian dates in the selected month the moon occupies each mansion.
- Month navigation (±24 months from today).
- Filter by electional rating: Highest / Prime / Good / Caution / Avoid / Veto.
- Moon phase label for each occupied date range.

## Files

| File | Purpose |
|------|---------|
| `index.html` | Practitioner / internal view |
| `28mansionsclientfacing.html` | Client-facing view (same algorithm) |
| `vercel.json` | Cache headers and clean URL config |
| `docs/algorithm.md` | Astronomical formula documentation |
| `docs/sop-deploy.md` | Deploy checklist |
| `CLAUDE.md` | Instructions for Claude Code — read before editing |

## Deploy

Vercel auto-deploys on push to `main`. No build step — pure static HTML.

```bash
git add .
git commit -m "your message"
git push origin main
# Vercel picks it up in ~30 seconds
```

To preview before merging:

```bash
git checkout -b fix/your-change
git push origin fix/your-change
# Vercel generates a preview URL automatically
```

## Astronomical Basis

Uses Abu Ma'shar's tropical framework: 28 equal mansions of 12°51'26" each, beginning at 0° tropical Aries. Moon longitude computed via a simplified Meeus series (Ch. 47) — accuracy ±1–2°, adequate given each mansion spans 12.857°.

**Framework: tropical zodiac, not sidereal.** Do not change without reading `CLAUDE.md`.

## Author

**Minhaaj Rehman** · [cciaf.minhaaj.com](https://cciaf.minhaaj.com)

## Contributing

Read `CLAUDE.md` before making any changes. Factual accuracy is the primary constraint — this tool is used directly by clients.
