# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A static, no-build interactive explainer site about probability, written for a Substack blog. Plain HTML/CSS/vanilla JS — no package.json, bundler, framework, or tests.

- `index.html` — landing hub: a central "Probability, visually" circle with tilted topic cards that fly out of it. Only the 5 `live` cards are shown. Each has a `.card-no` badge giving the reading order, clockwise from top-left: 1 Basics → 2 Types → 3 Expected Value & Variance → 4 Discrete → 5 Continuous (the user chose the textbook order: every distribution page reports μ and σ², so EV comes before them). The DOM order matches, so the mobile grid reads in the same order. The `soon` cards (Bayes, LLN, CLT) are still in the markup but hidden by `.card.soon { display: none; }` (user's request, 2026-10-04), and the script only collects `.card:not(.soon)`, so they get no threads. Removing that CSS line brings them back (they would shake and show a toast), but their old positions overlap the new layout.
- `basics.html` — the Probability Basics explainer (Stages 0–7: experiments/outcomes/sample spaces (multiplication rule), events, classical probability (equally-likely trap: dice totals, two coins), frequency probability (thumbtack, 1/√n error), subjective probability (Dutch book with $100 tickets), Kolmogorov's axioms (+ P(∅) = 0, complement rule, monotonicity), random variables (two-dice grid → PMF + CDF), together (concept map, cheat sheet, which meaning fits, common mistakes)). Union/addition rule is deliberately left out (see types.html note).
- `ev.html` — the Expected Value & Variance explainer (Stages 0–6: expected value (die / raffle / roulette / insurer games with running average), linearity (aX + b, total of n dice, sixes via indicators; birthday pairs and hat-check in the steps), variance (draggable die + per-face (x − μ)²P(x) contributions, shortcut E[X²] − μ²), sums & covariance (two dice: independent / half copy / copy / 7 − X), EV vs risk (three +$5 bets costing $45, exact Binomial P(behind) after n plays), continuous (uniform / exponential / triangle, area under x·f(x)), together (map, cheat sheet, mistakes)).
- `discrete.html` — the Discrete Probability Distributions explainer (Stages 0–6: Foundations/PMF/CDF/mean, Uniform, Bernoulli, Binomial, Negative Binomial incl. Geometric as r = 1, Poisson incl. Binomial→Poisson limit, Family map + cheat sheet).
- `continuous.html` — the Continuous Probability Distributions explainer (Stages 0–5: Foundations (bars→PDF, probability = area, CDF), Uniform, Exponential (incl. memoryless demo + Poisson link), Normal (incl. 68-95-99.7, z-scores, Binomial→Normal), Gamma (whole k only), Family map linking to discrete.html).
- `lln.html` — the Law of Large Numbers explainer (Stages 0–4: the idea + running share of heads, why it works (σ/√n + Chebyshev), gambler's fallacy, when it fails (Cauchy lighthouse), at work (roulette, weak vs strong law)).
- `clt.html` — the Central Limit Theorem explainer (Stages 0–6: the idea (average n draws from a die / yes-no / lopsided wait / U-shaped parent, histogram + CLT curve), standard error (n = 1, 4, 16, 64 small multiples, average vs sum − nμ), why a bell (exact convolution of the total with the normal curve, skew/√n), using it (z-scores; exact vs CLT vs half-step continuity correction for the average of n dice), when it fails (die vs Pareto α = 1.5 vs Cauchy, middle-50% width), at work (100 poll confidence intervals, coverage), together (map, cheat sheet, common mistakes)).
- `bayes.html` — the Bayes' Theorem explainer (Stages 0–5: conditional probability, Bayes' theorem (1,000-person test grid + count tree), base rate fallacy, updating again (odds form, Monty Hall), learning a coin's p (posterior curve), at work (naive-Bayes spam filter, prosecutor's fallacy)).
- `types.html` — the Types of Probability explainer (Stages 0–5: marginal (definition, unconditional ≠ independent, discrete + continuous formulas, why "marginal", ML uses), joint (table + Venn, bounds, real-vs-independent table, continuous draw-a-box, chain rule, ML uses), conditional (zoomed table + tree, multiplication rule), independence (mosaic with fixed margins), law of total probability (three machines → Bayes), together (Simpson's paradox, rule map, cheat sheet)). The Union & Complement stage was removed at the user's request: they want to study it first and add it back later. The last full version (section, panel, Venn step-by-step card, birthday card and their script) is saved in `_drafts/types-with-union.html`; restoring it means renumbering Independence/Total/Together back to 4/5/6 and re-adding its nav link, map node, cheat-sheet rows and the `STREAM` table. One running 2×2 survey table (Coffee/Tea × Morning/Night, 200 people). The "meanings of probability" (classical/empirical/subjective/axiomatic) and events/sample spaces live on basics.html.

**Locked for v1 (2026-10-02): `bayes.html`, `lln.html`, `clt.html`.** On the hub their cards are switched back to `soon` ("Soon" tag, `href="#"`, the real page kept in `data-href`, no "Open explainer" line), so visitors can't open them; the files stay in the folder. Links into them from other pages were turned into plain text (basics.html: two Bayes and one LLN mention; types.html: the conditional-stage Bayes mention and the rule-map Bayes node, which now says "coming soon"). Unlocking should restore those links too. To unlock one: set the card back to `live`, move `data-href` into `href`, set the tag to "Live", re-add the go line, add a `.card-no` badge, give it a free position, and bump the `.foot` count. Don't edit the locked pages unless the user asks.

Every page's `<style>` started as a copy of discrete.html's CSS (basics.html, clt.html, ev.html, lln.html, bayes.html and types.html have it verbatim, plus a page-specific block at the end), so a shared style change must be made in every unlocked page (ask before touching a locked one).

## Running

Open any page directly in a browser, or serve the folder:

```
python -m http.server 8765   # then http://localhost:8765/index.html
```

On this machine, a server started as a background Bash job gets killed within moments (exit 127). Start it as a detached process instead: `Start-Process python -ArgumentList '-m','http.server','8765' -WindowStyle Hidden` (PowerShell), and stop it later with `Stop-Process`.

Browser-automation tools can't open `file://` URLs, so use the server for automated checks. `html` has `scroll-behavior: smooth`, so in automated checks scroll with `window.scrollTo({top, behavior: 'instant'})` before taking screenshots. Append `?v=N` to the URL to get around the cache. The user often watches the same Chrome tab, and `.ex-try` buttons swap a stage's data (e.g. the card deck replaces the survey), so run interaction tests in a new tab and close it afterwards. Screenshots from the browser tools have come back blank or cropped on this machine; DOM/JS checks (reading the `-area` read-outs, clicking `.ex-try` buttons, measuring element boxes) are the reliable way to verify. Quick JS syntax check of every page without a browser (the extracted script goes to a temp file; in Git Bash use `$TEMP`, since Python and Node resolve `/tmp` to different places):

```
for f in index basics clt ev discrete continuous lln bayes types; do python -c "import re,sys;s=open(sys.argv[1]+'.html',encoding='utf-8').read();open(sys.argv[2],'w',encoding='utf-8').write(re.findall(r'<script>(.*?)</script>',s,re.S)[-1])" $f "$TEMP/x.js" && node --check "$TEMP/x.js" && echo "$f ok"; done
```

External deps are CDN-only: Google Fonts (Fraunces / Inter / JetBrains Mono) and KaTeX 0.16.9 from cdnjs (auto-render with `$$…$$` and `\(…\)` delimiters). Offline, formulas show as raw TeX.

## Architecture of discrete.html

Everything is in one file: a `<style>` block, the `<section>`s inside `<main>`, then (after `</main>`) the `#tip` tooltip, the quiz modal, the worked-examples sidebars, and one `<script>`.

### Page layout

Section ids, in order: `basics`, `uniform`, `bernoulli`, `binomial`, `negbin`, `poisson`, `family`. The nav `href`s, the family-map `target`s and the quiz keys all depend on these ids. Each distribution section (Stages 1–5) has the same order:

intro (PMF named in the prose) → conditions `.rules` with "Breaks if:" lines → `.example` → interactive `.card` (sliders, PMF `BarChart`, legend, chips + buttons, then the "Cumulative · P(X ≤ x)" `CdfChart`) → "Reading the graph" `.rules` ("When p goes up ↑" style cards) → optional derivation → `.formula` box (`PMF` / `CDF` labels on the left, `Summary` μ/σ² on the right) → `.term-key` "What each piece means" → two `.ex-launch` rows: Worked examples, then Quiz.

Stage 0 (Foundations) has a draggable die and the same CDF/examples/quiz, but no conditions or term key. Poisson also has a derivation (Binomial with n slices → Poisson) and a second card, the Binomial(n, 3/n) → Poisson(3) limit (`c5b`, `bp-*`), which has no CDF. Stage 6 has the family map and a static cheat-sheet table.

### Element ids per stage

| Stage | Prefix | PMF / CDF svg | Extras |
|---|---|---|---|
| 0 Foundations | `c0…` | `c0` / `c0cdf` | `c0-res` result line, `c0load` / `c0fair` |
| 1 Uniform | `u-` | `c1` / `c1cdf` | `u-stats` live chips in the formula box |
| 2 Bernoulli | `b-` | `c2` / `c2cdf` | `b-coin`, `b-n` "flip n times" input |
| 3 Binomial | `n-` | `c3` / `c3cdf` | `n-trials` dots, `n-shape` caption from `describeShape()` |
| 4 Negative Binomial | `nb-` | `c4` / `c4cdf` | `nb-trials`, `nb-geo` ("Set r = 1" = Geometric) |
| 5 Poisson | `po-` | `c5` / `c5cdf` | `po-tl` one-hour timeline |

Sliders are `${prefix}${param}` with a `-v` value label (e.g. `nb-r`, `nb-r-v`); results go to `${prefix}res`.

### Script

The script runs top to bottom, and each stage is an IIFE that runs straight away. So a `const` helper must be defined above the first IIFE that uses it: `LF`/`logC` (log-factorials) sit between Stage 3 and Stage 4, and `poissonPmf()` sits just before Stage 5.

- **Shared helpers:** `mk()` builds SVG elements, `fmt()`, `niceMax()` picks the y-axis max, the single `#tip` tooltip (`showTip`/`hideTip`), `stats()` gives mean/variance, `sample()` draws from a PMF.
- **`BarChart(svg, opts)`** is the chart every stage uses. It re-renders the whole SVG on every `render(state)` call. State: `{xs, p, emp?, dots?, mean?, sd?, hl?(i)→'hl'|'dim'|'', names?}`:
  - `emp`: orange "what actually happened" ticks
  - `dots`: overlay circles
  - `mean` / `sd`: dashed mean line with a fulcrum triangle, and the μ ± σ band
  - Options: `H`, a fixed `yMax`, `tip(i)` for the tooltip HTML, `onHover`, and `draggable` + `onDrag(i, v)` (used only by Stage 0's die, where `setBar` rescales the other bars so the total stays 1, with a cap of 0.6).
  - X labels thin out automatically by bar count. Per-bar value labels show only up to 12 bars, and are hidden when `emp` or `dots` is set.
- **`CdfChart(svg, {onHover})`** draws a step chart via `render(xs, p, upTo)`. Hovering a step sets the stage's `upTo`, and the stage's `hl` then lights bars `≤ upTo` and dims the rest. It thins x-labels past 16 points, and past 6 terms the tooltip collapses the sum to `P(X = a) + … + P(X = b)`.
- **One IIFE per stage** owns its sliders, simulation state (`counts`, `total`, `last`, `focus`, `upTo`) and buttons. The shared pattern: a slider's `oninput` calls `clear()` then `render()`. `render()` redraws both the PMF and the CDF. Simulations update the empirical frequencies (`emp = counts / total`). Every stage's `hl` uses the same priority: CDF hover (`upTo`) > example `focus` > last result. Infinite-support PMFs (Negative Binomial, Poisson) are cut off once the cumulative probability reaches about 0.995–0.999, with a cap of 150 bars, so their CDFs end slightly below 1.
- **Animations use timers, not `requestAnimationFrame`:** Binomial and Negative Binomial "Run experiment" use `setInterval`, and Poisson "Watch an hour" uses `setTimeout`s that finish after about 1.85 s. In automated checks, wait for them or use the ×500 buttons, which are synchronous.
- **Worked-examples sidebars (Stages 0–5):** there is one `.ex-panel` per stage (`fx/ux/be/bx/nbx/px-panel`) and a shared `#ex-scrim`. They are opened by `.ex-fab[data-panel]` buttons; open state is `.ex-panel.on` + `body.ex-open`, and a generic controller near the end of the script runs them. Each stage IIFE registers `exLoad[panelId] = dataset => chartId`, which:
  - sets its sliders from stage-specific attrs (`data-die`, `data-n`, `data-p`, `data-r`, `data-l`)
  - stores a `focus` predicate from `data-k` + `data-mode` (`eq`/`le`/`ge`/`mult`, via `exFocus`)
  - writes the result line via `exMsg`, or uses `data-msg` if given. `exMsg` computes "at least" as 1 − the rest, so a cut-off tail doesn't shrink the answer.

  The controller then scrolls to that chart's card. `clear()` and the run buttons reset `focus`.
- **Quizzes (Stages 0–5, keys `basics/uniform/bernoulli/binomial/negbin/poisson`; none for the Family map):** one shared centred dialog (`#qz-modal`, `#qz-scrim`, open state `body.qz-open`), opened by `.qz-fab[data-quiz=key]` buttons.
  - Content is data in `QUIZZES[key] = {stage, qs}`. Each question has:
    - `type`: `mc` (text options, shown 2×2), `charts` (each option is a mini chart) or `pick` (tap a bar)
    - `q`, and `vis` (`{xs, p, hide?, hl? (index or array), cdf?, mean?}`)
    - optional `after` (the vis shown once answered)
    - `opts`, `ans` (index), `why`, and `short` (the end-screen review label)
  - Visuals are drawn by `qzChart(svg, vis, {small, pick, cls})`, which is separate from `BarChart`.
  - `q` and `why` may contain `\(…\)` math, rendered with `katexIn`. In the JS strings, backslashes are doubled (`\\(`).
  - The end screen shows a score ring and a ✓/✗ review list, and its message names `quiz.stage`.
  - The colours `--qz-good`/`--qz-bad` are used only by the quiz.
- **Family map (Stage 6):** `nodes` (`{x, y, w, label, sub, target, desc, ext?}` on a 640×400 viewBox) and `edges` (`[from, to, label]`). `target` is the section id behind "Go to section". `ext: true` (Normal) marks a node that isn't on this page.
- The sticky nav highlights the section in view using an IntersectionObserver on `section[id]`.

### Adding a distribution

Several separate places must stay in step:
- the section (following the layout above) and its nav link
- a stage IIFE with a `CdfChart` and an `exLoad` entry
- an `.ex-panel` sidebar
- a `QUIZZES` entry plus a `.qz-fab` button
- a family-map node/edge
- a cheat-sheet row

## Architecture of continuous.html

It has the same single-file layout and helpers (`mk`, `fmt`, `#tip`, family map, nav observer), plus `erf`/`Phi`/`normPdf`/`gammaPdf`/`gammaCdf` (gamma CDF uses the Poisson-sum form, integer k).

- **`CurveChart(svg, {H, tip(x), onHandle(i, x), step})`** re-renders on `render(state)`. State: `{x0, x1, yMax, f?, bars?, shade?[a,b], hist?, mean?, handles?[c,d], ticks?, pins?, marks?, bracket?, past?, extra?, curveCls?}`. Handle dragging is tracked on the `<svg>` itself (pointer capture), because the handle elements are recreated on every render; keyboard arrows move a focused handle and it is refocused after re-render.
- **`ContStage(cfg)`** builds one distribution card from element ids `${id}-{chart,cdf,area,cdfnote,chips,shape,res,1,many,r}` and sliders `${id}-${param}` (+ `-v` value label). It owns the interval `[c, d]`, the samples (histogram drawn as density) and the linked CDF chart (marks + left-edge bracket = F(d) − F(c)). A handle at the axis edge means "and beyond" (area uses 0 / 1, label becomes `P(X ≤ d)` / `P(X ≥ c)`); `floor: true` marks x0 as a real lower bound. It returns `{P, render, interval, setInterval, load(params, c, d, msg)}`.
- **Empirical rule (Normal):** a standalone `er-` card after "The CDF has no simple formula": its own SVG (no `CurveChart`), with k pills `#er-k`, example pills `#er-ctx` (standard / heights 170±8 / exam 70±10 / IQ 100±15), slice bands, ±kσ brackets and Sample ×1000 chips. It is preceded by a plain-words explanation (three rule cards plus an `aligned` formula) and followed by "Deriving the rule" (`.lln-steps`: PDF integral → substitute z, so σ cancels → symmetry gives 2Φ(k) − 1 → series for k = 1), "The rule slice by slice" and "Using the rule" cards. Edit TeX in this file with the Edit tool: a Python heredoc in Git Bash once turned `\\` row breaks into `\`; the formula box and term key also show 2Φ(k) − 1.
- **Derivation boxes (Normal, Gamma):** below each stage's worked-examples button, a small `.ex-fab[data-panel]` opens a centred `.dv-modal` (not a sidebar), run by the same sidebar controller (scrim, Esc, ✕). Figures use the shared top-level `dvPanel(svg, px, py, pw, ph, x0, x1, y0, y1)` → `{sx, sy, line, area, lbl}`. CSS prefix `dv-`.
  - Normal `#dv-modal`: Herschel's dart argument → h(x) = −cx² → Gaussian integral via polar rings → mean 0 / variance 1 → X = μ + σZ (`dv-f1`…`dv-f5`; f1 is a seeded scatter, f5 has `dv-mu`/`dv-sd` sliders).
  - Gamma `#gd-modal`: sum of k Exponential gaps → CDF via Poisson count → derivative telescopes to λ·p_{k−1}(x) → "k − 1 events, then one in dx" → Γ(k) = (k−1)! → mean/variance/mode (`gd-f1`…`gd-f5`; f1–f3 share one fixed run `EV` with λ = 1, f5 has `gd-k`/`gd-lam` sliders and a dynamic x-range).
- **Quizzes (Normal, Gamma):** a `.qz-fab[data-quiz]` under each derivation button opens discrete.html's centred quiz modal (`#qz-modal`; quiz CSS and `--qz-*` tokens copied verbatim). The engine is discrete.html's, but with `qzCurve(svg, v, {small})` in place of `qzChart`: `vis`/`after`/chart options are Normal bells `{mu, sd}` or, when `k` is set, Gamma curves `{k, lam}`, plus optional `x0, x1, yMax, shade, ticks` (small charts default to −4..4); there's no `pick` type. `QUIZZES` has `normal` and `gamma` (6 questions each). The quiz's ✕ uses `addEventListener`, because the sidebar controller later sets `onclick` on every `.ex-close`.
- Gamma's λ slider starts at 1 (not 0.5), so that k = 12 still fits the fixed 0–20 axis.
- **Worked-examples sidebars:** one `.ex-panel` per stage (`u/e/n/g-panel`), opened by `[data-panel]` buttons, a single shared `#ex-scrim`, and open state `.ex-panel.on` + `body.ex-open`. Each `.ex-try` has `data-stage`, `data-set` (JSON slider values), `data-c`, `data-d` and an optional `data-msg`, and calls that stage's `load()`. Normal examples are standardised to μ = 0, σ = 1, because the chart axis is fixed at −10..10.

## Architecture of lln.html and bayes.html

These two pages share a lighter structure: the same helpers (`mk`, `fmt`, `big`, `#tip`, nav observer), one IIFE per stage, and a single worked-examples sidebar in the last stage (`#lx-panel` / `#bx-panel`, open state `body.ex-open`). "Try it" works through a registry: each stage IIFE sets `tryIt[name] = () => {…}`, and `.ex-try[data-go=name]` closes the panel and calls it. There are no quizzes or CDF charts on these pages.

- **lln.html:** `RunChart(svg, opts)` draws running values (`series: [{vals, n, cls, dot?}]`, values indexed 1..n) on a log x-axis from 1 to `opts.N`. It clips y to `[yMin, yMax]` and supports an optional `band(n) → [lo, hi]` funnel, a `target` line and a hover `tip(n)`. It reads `opts` on every render, so mutating `opts.target` works. `Animator(onFrame)` + `nextN(n, N, k)` grow n geometrically, so every decade takes the same time. The animations use `requestAnimationFrame`, which pauses in background tabs, so stub it in automated checks (e.g. `window.requestAnimationFrame = f => { queueMicrotask(f); return 1; }`).
- **bayes.html:** page-specific classes are prefixed `bz-`. Segmented buttons use `seg(id, onPick)`, which returns a setter. Stage 4's posterior is computed on a 400-point grid as ∝ p^a(1−p)^b (flat prior or Beta(20, 20)), normalised numerically, with the 95% range taken from the cumulative area. All interactions are synchronous: there are no animations.

## Architecture of basics.html

Built like discrete.html (same CSS verbatim incl. quiz styles, `BarChart`/`CdfChart`/`stats`/`sample` and the quiz engine copied from it), plus a page-specific CSS block prefixed `pb-`. Section ids: `experiments`, `events`, `classical`, `frequency`, `subjective`, `axioms`, `variables`, `together`.

- `TileGrid(svg, {tip, onHover, onClick})` draws outcome boxes: `render({rows, cols, main(r,c), sub?, cls?, rowHead?, colHead?, corner?, cell?})`, classes `in`/`hl`/`last`/`dim`. Two-dice tile index i ↔ `pair(i)` = (red, blue).
- Stage 2 also has: die-based "Where counting comes from" steps (x per face → 6x = 1 → |A|/n), a "bag of tickets" walk-through (11 tickets 1,2,1,1,3,3,4,4,3,4,6 = `TICKETS`; static `.pb-bag` strip + `.pb-group` table; labels ≠ outcomes), and a combinations (nCr, two aces = 6/1326) step list. The `cl-` card has a third mode `bag` (1×11 tiles, xs [1,2,3,4,6]; the slider hops over 5). Its panel has 5 examples (the committee nCr one has no Try-it), and its quiz has 6 questions.
- Stage 3 also has: "From tallies to probabilities" steps (one seeded run, seed 14: 12 rolls tallied, then a `.pb-conv` table at n = 12/120/1,200/12,000; the numbers are hard-coded from that run), and a second card `fd-` (all six faces, fair / loaded 0.14×5 + 0.3; `BarChart` of shares with true-p dots + `RunChart` in `series` mode with one line per face, the hovered/followed face drawn last in accent, others `fd-dim`; cap 100,000 rolls). `RunChart.render` accepts `{series: [{shares, cls, name}], targets}` (no band) besides the thumbtack's `{shares, target}`. Its panel has 5 examples (`fFaces`, `fLoaded` added), and its quiz has 6 questions.
- Prefixes: Stage 0 `s0-`, 1 `e1-`, 2 `cl-`, 3 `fq-` (`RunChart`, log x-axis, p ± 1/√n band; thumbtack's hidden p = 0.62, cap 100,000 trials), 4 `sj-` (stacked ticket-price bar + `.pb-cash` table), 5 `ax-` (six `step="any"` sliders built in JS, range −0.2..1; signed bar chart; axiom checks), 6 `rv-` (grid + PMF + CDF; lit bars priority: CDF hover > bar/tile hover > example focus > last roll), 7 family-map code (`fm`) with an `ext`/`href` Distribution node.
- Pills use `seg(id, onPick)` (returns a setter) on `.pb-pills` buttons; `unpick(id)` clears them for custom states.
- Worked examples: one `.ex-panel` per stage `#s0-panel`…`#s6-panel`, 3 each; `.ex-try[data-go]` calls `tryIt[name]()`, which returns the svg id whose card to scroll to. Quizzes: `QUIZZES` keys = section ids of Stages 0–6, 4 questions each. `--qz-good/--qz-bad` are also used here for the axiom checks and Dutch-book losses.
- All interactions are synchronous (no timers or rAF).

## Architecture of clt.html

Same skeleton as basics.html (discrete.html CSS verbatim + page block prefixed `ct-`; `mk`/`fmt`/`niceMax`/tooltip and the quiz engine copied from discrete.html; `seg()` pills, `tryIt` registry returning the svg id to scroll to). Section ids: `idea`, `spread`, `shape`, `using`, `limits`, `work`, `together`. No `BarChart`/`CdfChart`.

- `NumChart(svg, {H, tip(x)})` is a numeric-axis chart: `render({x0, x1, yMax, bars: [{x, w, h, cls}], curve(x), curveCls, shade: [a, b] (area under curve), band: [a, b] (full-height strip), vlines})`. Bars are in data units, so histograms use density (count / (N·w)) to sit under the normal curve.
- `binWidth(range, n, discrete)` keeps averages of discrete parents on lattice-aligned bins (multiples of 1/n) so bins don't alias; `histBars(vals, lo, hi, w)` builds density bars centred on lo + j·w.
- `PARENTS` (Stages 0–1: die, yes/no p = 0.2, exponential mean 4, arcsine U-shape). `convolve`/`sumPmf`/`pmfStats` give exact totals (Stages 2–3; the "only 1 or 6" parent has lattice step g = 5, so bars are drawn at height prob/g). `erf` is Abramowitz–Stegun 7.1.26; `Phi`, `npdf`.
- Prefixes: Stage 0 `i0-`, 1 `s1-` (4,000 samples per row), 2 `h2-` ("misplaced" = half the L1 gap between exact bars and the curve's mass per lattice cell), 3 `u3-` (n, a, b sliders; a ≤ b enforced), 4 `l4-` (4,000 averages; middle-50% width vs n = 1), 5 `w5-` (Wald intervals; Try-it runs 10 batches of 100, draws the last 100), 6 family-map code with an `ext` LLN node linking to lln.html.
- Simulations are synchronous `Math.random` (no timers), so numbers vary per run; check exact values (Stages 2–3) instead. Background tabs throttle `setTimeout`, so don't `await` timers in automated checks.
- Hub layout (all live cards `--w:260px`): Basics (14%, 23%), Types (50%, 14.5%), EV (86%, 23%), Discrete (74%, 81%), Continuous (26%, 81%). These were checked for no overlaps with each other, the circle or the header.

## Architecture of ev.html

Same skeleton as clt.html (CSS block prefixed `ev-`; `seg()`, `tryIt`, quiz engine), but it also copies discrete.html's `BarChart`/`CdfChart`/`stats`/`sample` (lines from `NS` to `sample`), since Stage 2 reuses the draggable die (`setBar`, cap 0.6) and Stage 3 the bar chart with mean/σ band. Section ids: `expected`, `linearity`, `variance`, `sums`, `risk`, `continuous`, `together`.

- `NumChart` here is the clt.html one plus bar labels (`lbl`), `vlines[].cls` (`mean-line` with fulcrum, `ev-zero`, `ev-target`) and an `ev-band` strip. `StemChart(svg, {H, money})` draws one stem per outcome at its numeric value with the balance-point triangle (Stages 0–1). `rangeOf(xs, minW)` pads an axis range.
- Prefixes: `e0-` (running average on a log axis, cap 200,000 plays), `l1-` (one svg shared by a `StemChart` and a `NumChart`; modes `lin`/`sum`/`six`), `v2-`, `s3-` (joint pmf `j(x, y)` per relation → total pmf and covariance), `r4-` (bets cost $45: A sure $50, B 50% × $100, C 10% × $500; exact binomial via `lgam` log-factorials up to 2,000), `c5-` (sliders built per shape in `#c5-ctl`).
- No links to the locked pages (Bayes, LLN, CLT); the LLN and CLT ideas are named in plain text only.

## Architecture of types.html

Same light structure as bayes.html (shared helpers, `seg()`, `tryIt` registry, no quizzes, page-specific CSS block at the end prefixed `tw-`/`vn-`/`ms-`/`tp-`). Section ids: `marginal`, `joint`, `conditional`, `independence`, `total`, `together`.

- 2×2 data objects `{title, rows, cols, n: [[a, b], [c, d]]}`: `SURVEY` (default), plus worked-example tables: `EMAIL`/`SHOP`/`FRAUD` (0), `COMMUTE`/`GYM`/`CONF` (1), `ONLINE`/`CART`/`KIDS`/`SCAN` (2), `DICE` (3); `tot(d)` gives row/column totals and N. Stages 0–3 hold a `data` variable that worked examples swap.
- `TwoWay(svg, {onPick(kind, i, j), tip})` draws the table; `render({data, cls(kind, i, j), txt(kind, i, j, v), heat})`, kinds `c` (cell), `r` (row total), `k` (column total), `g` (grand total), classes `hl`/`pick`/`dim`.
- `Venn(svg, {onPick(region)})` draws two fixed circles; regions `a`, `ab`, `b`, `n`. `regionCell(r, ai, bj)` maps a region to a table cell for A = row ai, B = column bj.
- Stages 0 and 1 also have continuous height × weight scatter cards (`m0c-*` marginal histograms on the edges; `j1c-*` draw-a-box joint probability, exact volume from a Simpson's-rule bivariate-normal integral, box at the axis edge = −∞/∞). Points are fresh `Math.random` Box–Muller samples, so in-box counts vary per load; check the exact values instead.
- Stage 1's `j1i-*` card toggles the survey against its independent version (each cell = row total × column total ÷ N).
- Stage 3's slider moves the top-left cell while keeping the margins fixed (range `max(0, R0 + C0 − N)`…`min(R0, C0)`); it drives both a contingency table (`i4-tbl`, counts/probabilities toggle, real vs predicted per cell) and the mosaic split graph.
- Stages 0 and 1 end with "… in machine learning" and "Common mistakes" `.rules` grids; the user asked for ML applications per concept, so keep that pattern for later stages.
- One worked-examples panel per stage, `#tx0-panel` … `#tx4-panel` (Stage 5 has none; its Simpson walk-through already is one), opened by that stage's `.ex-fab[data-panel]`, with a shared `#tx-scrim`; open state `.ex-panel.on` + `body.ex-open`. A panel holds only its own stage's examples. The user asked for 3–4 examples per stage, each with its own scenario (no reusing one dataset across stages, no repeating the stage's own example) and a working "Try it" button that loads that stage's card (`tryIt[name]`, e.g. `mEmail`/`mShop`/`mFraud`/`mHeight` in Stage 0, `jCommute`/`jGym`/`jConf`/`jTall` in Stage 1, `cOnline`/`cCart`/`cKids`/`cScan` in Stage 2). Stages 0–2 are done; Stages 3–4 (Independence, Total probability) still need expanding to 3–4 each.

## Conventions

- Light theme only. The user explicitly asked for a white background, so don't add a dark mode. Colors are CSS variables on `:root` (`--accent` orange #d9480f, `--bar` blue #2f5d8a), and all pages share the same palette and fonts.
- Section headings name the distribution or idea (e.g. `<h2>Binomial Distribution</h2>`, `<h2>The Base Rate Fallacy</h2>`), followed by an italic `.tagline` subtitle; the user asked for this. Explanations of a formula's origin go in a step-by-step `.rules.lln-steps` list ("Step 1 · …"), ending with a numeric check.
- Avoid short generic CSS class names. A `.t` class collision once broke the formula boxes; trial dots use `.tr`.
- Don't style bare descendant `span`/`div` inside containers that can hold `\(…\)` math: KaTeX's output is nested spans. For example, `.rule span {display:block}` once split inline math onto its own lines; it is now `.rule > span`.
- Each distribution section follows the same pattern: conditions cards with "Breaks if:" lines, an interactive card, "Reading the graph" cards, a formula box with PMF/PDF and CDF plus a "What each piece means" `.term-key`, and a worked-examples sidebar with "Try it" (discrete.html also adds a quiz; its full layout is under "Page layout" above). The user asked for PMF and CDF coverage in every distribution.
- Precompute any numbers quoted in prose or worked examples (e.g. with Python's `math.comb`) rather than doing the arithmetic by hand.
- Adding a topic to the hub: create its page, then in `index.html` switch the card from `soon` to `live`, set its `href`, change its tag text to "Live", add the `<div class="go">Open explainer <span>→</span></div>` line, and bump the "N topics live" count in `.foot`. Cards are positioned with inline CSS vars (`--x`, `--y` as percentages of the 1100×770 stage, `--r` tilt, `--w` width, `--d` entrance delay); positions were hand-tuned so no cards overlap each other or the circle. A live card is taller than a soon card, so re-check for overlaps after switching one: inject `*{transition:none!important;animation:none!important}`, add `ready` to `body`, then compare the `.card-in` and `.hub` bounding boxes. Below 860px the layout becomes a flowing grid.
- The user reviews the UI step by step: build one stage or feature at a time and let them judge before expanding scope.
