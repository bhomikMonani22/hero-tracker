# Hero Tracker — Yantrayug

Hero scheme **target vs achievement** tracker for Yantrayug Hero Hub. Single-file web app
(Vanilla JS + Tailwind) backed by Supabase, deployed on Vercel.

## What it does
- Tracks each retailer's RC code, Hero retailer name, **Marg name** (used for matching), **target**, and **achieved**.
- **Update tracker** — upload the Hero scheme sheet (`RC Code · Retailer · Overall · MARG`). Parties upsert by RC code; achievement is left untouched.
- **Upload Marg export** — upload the Marg party-wise sales file. Each party is matched by Marg name → fuzzy fallback. Unmatched rows are surfaced for one-click mapping (the mapping is saved as an alias so it auto-matches next time).
- **Inline editing** — edit target / achieved / Marg name / retailer name directly in the table for small changes; no re-upload needed.
- State lives in Supabase, so you only upload when the tracker or a Marg export actually changes.

## Stack
- Front end: `index.html` (Tailwind Play CDN, SheetJS, supabase-js v2)
- Back end: Supabase project `wqdpvalgkfqyfntalfxd`
  - `hero_parties` — core target/achievement rows (keyed by `rc_code`)
  - `hero_marg_aliases` — learned Marg-name → party mappings
  - `hero_meta` — last-upload metadata
- Host: Vercel (static)

## Local run
Just open `index.html`, or `npx serve .`

## Matching logic
Names are normalised (uppercase, `&`→`AND`, strip `W/O GST` & punctuation). Exact match on
the normalised Marg name, then retailer name, then a learned alias, then a token Dice
fuzzy match at ≥ 0.60. Anything below is shown as unmatched for manual assignment.
