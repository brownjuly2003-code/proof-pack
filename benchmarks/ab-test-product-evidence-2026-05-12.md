# AB Test Research Designer — verified product evidence, 2026-05-12

> Kind: `live · product`. All numbers below are direct UI output from the live HuggingFace Space deployment, captured via Playwright on 2026-05-10 against `https://liovina-ab-test-research-designer.hf.space`.
>
> The motivating `57%` headline is research-cited (Johari et al., KDD 2017, "Peeking at A/B Tests") — it is the textbook reason the tool exists; it is not what the tool itself emits.

- Source product: `https://liovina-ab-test-research-designer.hf.space`
- Repository: `https://github.com/brownjuly2003-code/AB_TEST` · HEAD `7a627459`
- Captured: `2026-05-10`

## Motivation (literature, not product output)

| number | meaning |
|---|---|
| `57%` | False-declaration rate of classical fixed-horizon A/B testing under continuous peeking, in simulation. Source: Johari et al., KDD 2017, "Peeking at A/B Tests" — cited via Optimizely Stats Engine whitepaper. |
| `>20%` | Pre-Stats-Engine industry error rate baseline (Optimizely 2015). |

## Verified product output — binary canonical (live wizard)

Config: `purchase_conversion`, baseline `4.2%`, MDE `7%`, α `0.05`, power `0.8`, 2 variants, 50/50 split, 7,200 effective users/day, 4 interim analyses.

| metric | value |
|---|---:|
| sample size / variant | `75,514` |
| duration | `21 days` |
| sequential adjusted sample / variant | `77,402` |
| z-boundaries (O'Brien-Fleming, 4 looks) | `4.05 / 2.86 / 2.34 / 2.02` |
| cumulative α spent at each look | `0.0001 / 0.0056 / 0.0236 / 0.0500` |

### SRM check (live tool, real numbers)

Input: observed `[24500, 25500]`, expected `[25000, 25000]` → 49.0% / 51.0% split.

| metric | value |
|---|---:|
| χ² | `20` |
| p-value | `8.0·10⁻⁶` |
| verdict | `SRM detected — check randomization or tracking` |

## Verified product output — continuous + CUPED + Bayesian + multi-arm

Config: `Continuous`, baseline mean `45.20`, std dev `12`, MDE `5%`, 4 variants (25/25/25/25), Bayesian framework, credibility `0.95`, CUPED `pre-exp std = 5`, ρ = `0.5` → ρ² = `0.25` → expected 25% variance reduction.

| panel | metric | value |
|---|---|---:|
| Live estimate | frequentist sample / variant | `591` |
| Live estimate | CUPED sample / variant | `443` |
| Live estimate | variance reduction | `25%` |
| Live estimate | multi-arm correction | `Bonferroni applied` |
| Report — CUPED card | before → after | `591 → 443` |
| Report — CUPED card | adjusted std dev | `10.3923` |
| Report — Bayesian card | frequentist | `591` |
| Report — Bayesian card | Bayesian 95% interval | `277` |
| Report — posterior plot | target sample / variant | `277` (95% credibility, half-width 2 units) |
| Report — posterior plot | distribution range | `41.12 – 49.28` |

## Trust layer

| metric | value |
|---|---:|
| backend tests | `351` |
| frontend tests | `200+` |
| coverage gate | `89%` |
| locales | `7 (including RTL)` |
| stack | FastAPI · React · SQLite · Playwright E2E · axe-checks |

## Public-safe claim

> AB Test Research Designer turns a hypothesis into a launch-ready plan: live wizard emits `75,514 / variant` and `21 days` for the canonical binary config; group-sequential O'Brien-Fleming boundaries (`4.05 / 2.86 / 2.34 / 2.02`) make interim looks safe; SRM detector catches a 49.0% / 51.0% split as `χ² = 20, p ≈ 8·10⁻⁶`; CUPED reduces continuous-metric sample size by `25%` (`591 → 443`); Bayesian credible interval lands at `277` per variant. The `57%` headline is the textbook reason the tool exists — Johari et al. KDD 2017 — not a self-measurement.
