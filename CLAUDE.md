# CLAUDE.md — groundtruth-site

## What this is

The one-page marketing site for the company (working/placeholder brand: **"Groundtruth"**)
that sells physics-grounded RL environments and evals for physical AI to labs and robotics
companies. It is a single self-contained `index.html` — no build step, no dependencies, no
external requests — hosted on **GitHub Pages from `main`** (push to `main` = deploy).

Art direction is modeled on scale.com (2026-07): light chrome + full-bleed pinned hero
(edge-to-edge animated canvas, no border/rounding), then a dark region with scroll-driven 3D
"stages" — tilted panels inside thin wireframe frames with dashed contour SVGs and binary
mono labels. All copy is original.

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

- **Benchmark table:** **SYNCED 2026-07-17** to the final published table (2026-07-13, 9
  models × 5 scenarios × 5 epochs, 25 episodes/model) from the now-public repo
  https://github.com/Jaiparmar940/rlenv. Page shows the top 7 of 9 rows (fable 86.0 /
  gpt 82.3 / grok 74.9 / sonnet 74.5 / gemini-3.5-flash 59.7 / haiku 39.9 / ministral-3b
  23.0; omitted: qwen-7b 20.9, llama-8b 7.2 — caption says "Showing 7 of 9 models").
  Per-row annotations are the real root-ok counts from the repo README ("root cause N/25").
  Score bars fill with scroll progress across the whole sticky section (0 → score% at the
  end of the animation).
- **"19-pt uncoached gap" / coaching-dependence failure mode:** from the original smoke A/B
  (haiku coached 57.4 vs uncoached 38.3). **STALE** — the closeout re-measurement under the
  current prompt pair is **13.6 points** (61.5 vs 47.9). Direction and story unchanged.
- **"4 physics bugs human-caught":** accurate. Jaivir (founder, domain verifier) caught four
  physics bugs that passed the automated suite: load-sag inversion; a downstream node reading
  above an upstream one; a red-herring battery drifting into genuinely-weak territory; a
  ground-fault drop too small to characterize. This is the core product story.
- **Hero terminal transcript (12.58 V rest / 10.41 V cranking / 2.75 V ground drop, scenario
  `medium_corroded_ground`, "seed 41"):** **SYNCED 2026-07-17** to post-recalibration
  physics. Story beats preserved: battery looks fine at rest, holds under load (passes the
  9.6 V load test), big battery_negative→engine_block drop under cranking is the tell. The
  seed is decorative. "score 100 / 100" is the genuine expert-path score. The same numbers
  appear in the hero canvas telemetry vignette (`sceneScope`) — keep the two in sync.
- **Failure modes list (supply-referenced blindness; framing the innocent part;
  charging-system confusion; coaching dependence):** first two are Jaivir's banked
  observations from human play; charging-system confusion matches the framed-alternator trap
  (resolved in rlenv by adding a `running` engine state). Real, but phrasing is the site's.
- **"100% computed physics / zero hand-authored voltages" and grading claims:** accurate by
  rlenv's construction (resistance network, V = I×R, seeded ±0.05 V noise; anti-gaming caps).

## Placeholders that are not real yet

- **Brand "Groundtruth"** — placeholder name (nod to ground faults + eval ground truth).
  Swap points: `<title>`, nav logo, footer logo, copyright line, and this file.
- ~~GitHub links~~ — RESOLVED 2026-07-17: nav button, footer link, and quickstart clone URL
  all point at the public repo https://github.com/Jaiparmar940/rlenv.
- `founders@groundtruth.dev` — domain not registered.

## How the page is built (for editing)

Everything is in `index.html`:

- **Hero art** is a fully animated `<canvas>` "deep-field drift" (chosen by Jaivir
  2026-07-18 from four candidates; earlier crystal-fan field, circuit-trace network, and
  telemetry/arm/lidar vignettes were all rejected as too busy/tacky): `buildDust()`
  (seed 29) scatters 260 dust motes with a depth value driving size, brightness, and
  parallax drift speed; `drawDust(t)` renders them with slow diagonal drift, individual
  twinkle, ~6% amber accents, over a faint slate radial glow. Deliberately calm — no
  charts, labels, or data imagery. Animation only runs while the hero is on screen;
  `prefers-reduced-motion` gets one static telemetry frame (and unpins the hero: shell
  height auto, copy2 shown statically at the bottom). The hero is a pinned 200vh section
  (`.hero-shell` > sticky `.hero-pin` > full-viewport card): as the user scrolls, the
  headline floats up/out, then `.hero-copy2` (the intro paragraph, which lives INSIDE the
  card — there is no light band between hero and dark region) floats into center, then
  drifts out as the card unpins straight into `.dark` (veil bottom blends to --bg so the
  seam is invisible).
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
