# CLAUDE.md — second-nature-site

## What this is

The one-page marketing site for the company (brand: **"Second Nature"**, chosen 2026-07-19;
previously placeholder "Groundtruth")
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

- ~~Brand "Groundtruth"~~ — RESOLVED 2026-07-19: renamed to **"Second Nature"** across
  `<title>`, meta description, nav logo, hero copy, footer logo, copyright, and this file.
  Icon is now a two-wave mark (faint first pass above, solid amber second pass below —
  "practiced until instinct", still signal-flavored): favicon data URI + inline SVGs in nav
  (dark rounded square) and footer (inverted, amber square).
- ~~GitHub links~~ — RESOLVED 2026-07-17: nav button, footer link, and quickstart clone URL
  all point at the public repo https://github.com/Jaiparmar940/rlenv.
- ~~`founders@secondnature.dev`~~ — RESOLVED 2026-07-19: that domain was never registered.
  Contact mailto is now `founders@snlabs.dev`, forwarded to jaiparmar940@gmail.com via
  ImprovMX (MX/SPF records live in GoDaddy DNS; briefly used jay.parmar@duke.edu in between).

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
  height auto, copy2 shown statically at the bottom). The hero is a pinned 220vh section
  (`.hero-shell` > sticky `.hero-pin` > full-viewport card; pin height is 100svh so mobile
  Safari centers against the visible viewport, and on ≤640px `.hero-copy` gets 56px
  bottom padding to offset the 60px nav above the card): as the user scrolls, the
  headline floats up/out, then `.hero-copy2` (the intro paragraph, which lives INSIDE the
  card — there is no light band between hero and dark region) floats into center, then
  drifts out as the card unpins straight into `.dark` (veil bottom blends to --bg so the
  seam is invisible). There is no announce banner above the nav (removed 2026-07-20).
- **Nav-height gotcha (`--nav-h`)**: the nav is `position: sticky`, so it occupies flow
  space *above* the pinned hero card — at scrollTop 0 the 100svh card hangs one nav-height
  below the fold, and anything anchored to the card's bottom edge lands off screen at the
  page's opening position. `--nav-h` (68px; 60px ≤640px) is the single source for that
  height: it sets `.nav-inner`'s height and is added back into `.scroll-cue`'s offset
  (`bottom: calc(28px + var(--nav-h))`), which is why "Scroll to explore" is visible on
  load at every viewport size — before 2026-07-20 it sat below the fold on laptops and
  phones. Use `--nav-h`, never a literal, for anything bottom-anchored inside the card.
- **Scroll motion** is one rAF loop (`frame()`): `stickyProgress()` maps each tall section
  (`.showcase` 165vh, `.term-section` 170vh) to 0–1; panels/wireframes interpolate
  rotateY/rotateX/translate via `lerp`, benchmark bars fill from progress. Tilt constants
  live in `frame()`. `prefers-reduced-motion` bypasses all of it. Section spacing was
  tightened 2026-07-20 after phone review (`.statement` is padded content, ~50–75vh, no
  longer a full 100vh; `section` 96px desktop / 60px phone) — resist re-inflating it.
- **Smooth scroll, gesture cap + snap assist** (reworked 2026-07-20): wheel events are
  preventDefault'd into a virtual `smooth.target`; the top of `frame()` eases the real
  scroll toward it with an exponential lerp (`smooth.tau`, 110ms for direct wheel input).
  Wheel events <300ms apart form one "gesture" (trackpad momentum included), and a
  gesture's target is hard-capped at the next section boundary ≥20% viewport ahead
  (`gestureBounds` = statement/environments/benchmark/platform/customers/quickstart/
  contact/founder) — one continuous scroll can land ON the next section, never through
  it. On wheel-idle (200ms), a showcase snaps in once its predicted landing (`finalTop`)
  enters ≥35% (`#environments`) / 25% (`#benchmark`), forward-only, at a pace tracking
  recent wheel velocity (τ 120–260ms) — but never when parked at/just past any boundary
  (that would chain-snap past the statement). In-page `#anchor` clicks ride the glide.
  Touch/keyboard/scrollbar stay native: a scroll event deviating >1.5px from
  `smooth.current` re-syncs and deactivates (so the cap cannot apply to touch; idle snap
  still can). All skipped under reduced motion.
- **Nav-link autoplay** (added 2026-07-20): clicking a nav link whose href is in
  `autoplayHrefs` (`#environments`, `#benchmark`) does not just park at the section top —
  that is the stage's *first* frame, deepest tilt, nothing filled in. The click glides to
  the top as before and sets `pendingAutoplay` to the sticky end
  (`top + offsetHeight - innerHeight`, i.e. `stickyProgress` 1); `frame()` starts the
  playback at the moment the glide converges. The playback is its own branch at the top of
  `frame()` — ease-in-out over a fixed duration (`~3.2s per viewport of travel`, clamped
  1.2–4.2s, so ≈2.2s for a real section) rather than the exponential glide, which would
  crawl through the final stretch where the payoff is. It writes `smooth.current/target` in
  step so the wheel and scroll handlers see a position they wrote. Any real input drops it
  (`cancelAutoplay`): a wheel (which then seeds a fresh gesture from the current spot),
  `touchstart`, `keydown`, or a scroll event deviating >1.5px. Other anchors are unchanged.
- **Phone layout (≤640px)**: `.bench-caption` is static in flow below the stage (it was
  absolutely positioned and overlapped the panel on phones); `.bench-stage` is
  `calc(100vw - 36px)` and `.term-stage` `calc(100vw - 48px)` so panels keep a real
  margin to the screen edge.
- **Terminal typing** (added 2026-07-19): `typeTerminal()` blanks the `.t-line` text nodes
  and retypes them char-by-char (agent lines 4 ms/char, env output 2 ms/char) with a
  blinking `.t-caret`. The first line (the `# complaint:` prompt) is exempt: it carries
  `.on` in the markup, is never blanked or retyped, and holds the parked caret so the
  terminal reads as waiting. Retriggered 2026-07-20 from the IntersectionObserver
  (threshold 0.7) to a SETTLE gate inside `frame()` — typing starts once the terminal
  stage's `stickyProgress` ≥ 0.75, i.e. three-quarters of the way through the stage's
  travel, so the transcript overlaps the tail of the tilt (the ease is quadratic-out, so
  the panel is already within ~6% of its resting angle there). Briefly 0.92 — a full
  settle — before Jaivir asked for the earlier start. Reduced motion shows all lines
  instantly. If the
  transcript copy changes, no JS updates are needed — it types whatever is in the markup.
- Stage scaffolding: `.stage` (perspective) > `.wireframe` frames + `.contours` SVG +
  `.bin` labels + content panel. Reuse these primitives for new sections.
- Known local-dev quirk: the Claude Code browser pane fails to capture screenshots at
  scrolled positions (returns blank frames; reproduces on scale.com too). Verify sections by
  temporarily hiding preceding DOM so the target sits at scrollTop 0, or use a real browser.

## Dev / deploy

- Local: open `index.html` directly, or `python3 -m http.server 8712` and browse.
- Deploy: GitHub Pages serves `main`. Custom domain **snlabs.dev** (GoDaddy, CNAME file
  added 2026-07-19): the github.io URL now 301s to it. As of 2026-07-19 12:30Z the GoDaddy
  DNS still pointed at GoDaddy's parking site (A 76.223.105.230/13.248.243.5) — until the
  apex A records are changed to GitHub Pages (185.199.108.153/.109/.110/.111) and Enforce
  HTTPS is enabled, the live site shows GoDaddy's placeholder, NOT the deployed page.
  Keep the page self-contained —
  no external fonts/scripts/images — so it works from any path (Pages subpath included; all
  internal links are `#anchors`).
- No analytics, no forms, no cookies. Keep it that way unless Jaivir decides otherwise.
