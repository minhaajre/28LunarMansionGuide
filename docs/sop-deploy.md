# SOP: Deploy

## Normal deploy (bugfix or content update)

```bash
git checkout main
git pull origin main
# make your changes
git add index.html 28mansionsclientfacing.html   # name files explicitly
git commit -m "fix: brief description of change"
git push origin main
```

Vercel auto-deploys `main` in ~30 seconds. Confirm at the Vercel dashboard or the live URL.

## Preview deploy (algorithm changes or anything client-visible)

```bash
git checkout -b fix/your-branch-name
git push origin fix/your-branch-name
# Vercel generates a preview URL — check it before merging
```

Verify on preview:
- [ ] Navigate to a date range you know (e.g., Jun 27, 2027 → M1 Aries)
- [ ] Check that today's month loads correctly
- [ ] Confirm no JS console errors
- [ ] Check on mobile viewport

Then merge to main:

```bash
gh pr create --title "fix: description" --body "What changed and why"
# or merge directly if solo:
git checkout main && git merge fix/your-branch-name && git push origin main
```

## Rollback

```bash
git log --oneline -10          # find the last good commit hash
git revert <commit-hash>       # creates a revert commit (safe, non-destructive)
git push origin main
```

Do NOT use `git reset --hard` on `main` — it rewrites history.

## Algorithm changes — extra gates

Before committing any change to `moonLon`, `toJD`, or `lonToMansion`:

1. Run the validation script:
   ```bash
   node -e "
   // paste moonLon, toJD, lonToMansion here
   // then check known reference dates
   console.log('Jun 27 2027:', lonToMansion(moonLon(toJD(2027,6,27,12)))); // expect 1
   console.log('Jun 26 2026:', lonToMansion(moonLon(toJD(2026,6,26,12)))); // expect 19
   "
   ```
2. Cross-check one more date against AstroSeek.
3. Apply the same change to BOTH `index.html` and `28mansionsclientfacing.html`.
4. Deploy to preview first.
