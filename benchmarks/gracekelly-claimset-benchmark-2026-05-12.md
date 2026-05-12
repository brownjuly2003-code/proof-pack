# GraceKelly claim-set benchmark — 2026-05-12

> Kind: `simulation`. 5 simulated model responses per case, with deterministic similarity from group labels. Live `gracekelly.core` modules (HAC clustering, cluster confidence, divergence assessment) run on the simulated matrices.
> This validates the consensus engine's mechanics, not live hallucination reduction.

- Cases: `35`
- Source repo: `D:\GraceKelly`
- Generated: `2026-05-12T05:26:30`
- HAC threshold: `0.85`
- Max rounds: `3`

## Aggregate

| metric | value |
|--------|------:|
| `unsupported_claim_rate` | 0.4444 |
| `unstable_claim_detection` | 1.0 |
| `false_consensus_rate` | 0.5 |
| `agreement_cluster_purity` | 1.0 |
| `abstention_quality` | 1.0 |

Action distribution: {"accept": 9, "debate": 9, "expand": 17}

## Per-category

| category | n | accept | debate | expand | escalate | match_expected | purity |
|----------|--:|------:|------:|------:|--------:|--------------:|------:|
| `ambiguous_split` | 5 | 0 | 0 | 5 | 0 | 1.0 | 1.0 |
| `contradictory` | 5 | 0 | 0 | 5 | 0 | 1.0 | 1.0 |
| `factual_strong` | 5 | 0 | 5 | 0 | 0 | 1.0 | 1.0 |
| `factual_unanimous` | 5 | 5 | 0 | 0 | 0 | 1.0 | 1.0 |
| `insufficient_data` | 4 | 0 | 0 | 4 | 0 | 1.0 | 1.0 |
| `trap_false_consensus` | 4 | 4 | 0 | 0 | 0 | 1.0 | 1.0 |
| `trap_majority_wrong` | 4 | 0 | 4 | 0 | 0 | 1.0 | 1.0 |
| `unstable` | 3 | 0 | 0 | 3 | 0 | 1.0 | 1.0 |

## Public-safe interpretation

- `unstable_claim_detection` = 1.0 — fraction of `contradictory`/`unstable` cases where the engine refused ACCEPT.
- `abstention_quality` = 1.0 — fraction of `insufficient_data` cases where the engine refused ACCEPT and surfaced for human review.
- `false_consensus_rate` = 0.5 — fraction of trap cases where all models agreed on a wrong answer and the engine still ACCEPTed. This is the known floor of unanimity-based consensus and is reported honestly, not hidden.
- `agreement_cluster_purity` = 1.0 — average purity of the top cluster (correctness-label homogeneity).

## Caveats

- No live LLM was invoked; responses are templated and similarities are synthesized from group labels.
- The engine has no ground-truth oracle, so unanimous wrong answers (trap_false_consensus) are accepted. The benchmark surfaces this limitation, it does not claim immunity from it.
- These numbers measure the mechanism's discrimination on contrived inputs, not its effect on real production traffic.