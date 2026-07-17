# CLAUDE.md — groundtruth-site

## What this is

The one-page marketing site for the company (working/placeholder brand: **"Groundtruth"**)
that sells physics-grounded RL environments and evals for physical AI to labs and robotics
companies. It is a single self-contained `index.html` — no build step, no dependencies, no
external requests — hosted on **GitHub Pages from `main`** (push to `main` = deploy).

Art direction is modeled on scale.com (2026-07): light chrome + full-viewport rounded hero
card, then a dark region with scroll-driven 3D "stages" — tilted panels inside thin wireframe
frames with dashed contour SVGs and binary mono labels. All copy is original.

## Relationship to `rlenv` (the product repo)

The company's first public artifact is **no-start-env**: an Inspect-native agentic RL
environment + benchmark where LLM agents diagnose simulated vehicle electrical no-start /
charging faults, scored by a cheat-resistant 0–100 grader (root cause 60 / parts discipline
25 / time efficiency 15; fixes must be verified by a successful start). It lives locally at
`~/Desktop/rlenv` (public GitHub repo pending as of 2026-07-17). Its `CLAUDE.md` and
`results/results.md` are the source of truth for every claim this site makes.

## Provenance of every number on the page — CHECK BEFORE ANY REAL LAUNCH

The site was drafted 2026-07-17 from rlenv state that has partially moved on. Items marked
STALE must be synced with `rlenv/results/results.md` before the site is promoted anywhere.

- **Benchmark table (fable-5 98.2 / sonnet-5 82.4 / grok-4 78.9 / gpt-5.5 71.0, "9 episodes"):**
  the 2026-07-12 k=3 run (4 models × 3 scenarios × 3 epochs, full-resolution grading).
  **STALE** — it predates rlenv's parser fixes round 3, the series-consistency physics
  recalibration, and the final published table (2026-07-13, 9 models × 5 scenarios × 5
  epochs: fable 86.0 / gpt 82.3 / grok 74.9 / sonnet 74.5 / gemini-flash 59.7 / haiku 39.9 /
  ministral-3b 23.0 / qwen-7b 20.9 / llama-8b 7.2; separation 78.8). The page hedges with
  "Preliminary; final table ships with the writeup," which is why this was acceptable for a
  draft. The per-row annotations ("red herring −16" etc.) are illustrative, not computed.
- **"19-pt uncoached gap" / coaching-dependence failure mode:** from the original smoke A/B
  (haiku coached 57.4 vs uncoached 38.3). **STALE** — the closeout re-measurement under the
  current prompt pair is **13.6 points** (61.5 vs 47.9). Direction and story unchanged.
- **"4 physics bugs human-caught":** accurate. Jaivir (founder, domain verifier) caught four
  physics bugs that passed the automated suite: load-sag inversion; a downstream node reading
  above an upstream one; a red-herring battery drifting into genuinely-weak territory; a
  ground-fault drop too small to characterize. This is the core product story.
- **Hero terminal transcript (12.58 V rest / 11.44 V cranking / 2.77 V ground drop, scenario
  `medium_corroded_ground`, "seed 41"):** illustrative episode consistent with the scenario
  spec **before** the 2026-07-12 series-consistency recalibration. Under current physics the
  cranking battery voltage is **~10.41 V** (still passes the 9.6 V load test; ground drop
  ~2.75 V unchanged). If updating, keep the story beats: battery looks fine at rest, holds
  under load, big battery_negative→engine_block drop under cranking is the tell. The seed is
  decorative. "score 100 / 100" is the genuine expert-path score.
- **Failure modes list (supply-referenced blindness; framing the innocent part;
  charging-system confusion; coaching dependence):** first two are Jaivir's banked
  observations from human play; charging-system confusion matches the framed-alternator trap
  (resolved in rlenv by adding a `running` engine state). Real, but phrasing is the site's.
- **"100% computed physics / zero hand-authored voltages" and grading claims:** accurate by
  rlenv's construction (resistance network, V = I×R, seeded ±0.05 V noise; anti-gaming caps).

## Placeholders that are not real yet

- **Brand "Groundtruth"** — placeholder name (nod to ground faults + eval ground truth).
  Swap points: `<title>`, nav logo, footer logo, copyright line, and this file.
- `https://github.com/groundtruth/no-start-env` (quickstart clone URL) and the nav GitHub
  button (points at github.com root) — update when rlenv's public repo exists.
- `founders@groundtruth.dev` — domain not registered.

## How the page is built (for editing)

Everything is in `index.html`:

- **Hero art** is generated at load by `paintHero()` — seeded PRNG (seed 41) drawing
  crystal-fan ray clusters on a `<canvas>`; deterministic per viewport size, no assets.
- **Scroll motion** is one rAF loop (`frame()`): `stickyProgress()` maps each tall section
  (`.showcase` 240vh, `.term-section` 200vh) to 0–1; panels/wireframes interpolate
  rotateY/rotateX/translate via `lerp`, benchmark bars fill from progress. Tilt constants
  live in `frame()`. `prefers-reduced-motion` bypasses all of it.
- Stage scaffolding: `.stage` (perspective) > `.wireframe` frames + `.contours` SVG +
  `.bin` labels + content panel. Reuse these primitives for new sections.
- Known local-dev quirk: the Claude Code browser pane fails to capture screenshots at
  scrolled positions (returns blank frames; reproduces on scale.com too). Verify sections by
  temporarily hiding preceding DOM so the target sits at scrollTop 0, or use a real browser.

## Dev / deploy

- Local: open `index.html` directly, or `python3 -m http.server 8712` and browse.
- Deploy: GitHub Pages serves `main` at the repo's Pages URL. Keep the page self-contained —
  no external fonts/scripts/images — so it works from any path (Pages subpath included; all
  internal links are `#anchors`).
- No analytics, no forms, no cookies. Keep it that way unless Jaivir decides otherwise.
