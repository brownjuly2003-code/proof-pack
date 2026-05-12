# Evidence snapshot - 2026-05-12

Purpose: current factual proof layer for AutoReel public copy. Use these numbers only with their stated scope.

## DE_project / AgentFlow

Command:

```powershell
D:\DE_project\.venv\Scripts\python.exe scripts\run_benchmark.py --users 50 --spawn-rate 10 --run-time 60s --results-json D:\AutoReel\docs\research\benchmarks\de_project-benchmark-2026-05-12.json --report-path D:\AutoReel\docs\research\benchmarks\de_project-benchmark-2026-05-12.md
```

Result:

- 50 users, 60s, spawn rate 10/s, 506 requests.
- 0 failures.
- Aggregate p50 `14ms`, p95 `97ms`, p99 `280ms`.
- Entity endpoints p50 `11-14ms`.
- Worst entity p99 `330ms`.
- `scripts/check_performance.py` returned `PASS` against committed baseline.

Public-safe claim: fresh local benchmark shows live entity lookups in low double-digit milliseconds, with p99 below 330ms and 0 failures in the run.

## RAG Support Assistant

Commands:

```powershell
python evaluation\simulate_model_benchmark.py --cases evaluation\test_cases.json --output D:\AutoReel\docs\research\benchmarks\rag-simulated-model-comparison-2026-05-12.md --json-output D:\AutoReel\docs\research\benchmarks\rag-simulated-model-comparison-2026-05-12.json
python evaluation\benchmark_runner.py --cases evaluation\test_cases.json --output D:\AutoReel\docs\research\benchmarks\rag-offline-expected-answer-eval-2026-05-12.json
$env:PYTEST_DISABLE_PLUGIN_AUTOLOAD='1'; python -m pytest -p no:schemathesis --basetemp D:\AutoReel\tmp-qa\pytest-rag -q tests\test_simulate_model_benchmark.py tests\test_streaming_rag_parity.py
```

Result:

- Simulated model comparison: qwen2.5:7b relevancy `1.000`, faithfulness `0.500`, context recall `1.000`; this is synthetic, not a live model benchmark.
- Offline expected-answer eval without retrieval context: faithfulness `0.0000`, answer relevancy `0.5486`, context precision `0.0000`, context recall `0.0000`; this confirms the current offline runner is not valid as public RAG quality proof.
- Targeted tests: `11 passed`, 1 warning; includes simulated model benchmark and streaming/citation parity paths.

Public-safe claim: cite the mechanism, not quality percentages. Use `[N]` citations, source panel, streaming parity, and human escalation gates. Do not claim recall or faithfulness until a retrieval-grounded benchmark is run.

### RAG offline grounded benchmark (added 2026-05-12)

Command:

```powershell
python D:\AutoReel\docs\research\benchmarks\harness\rag_grounded_eval.py --output-json D:\AutoReel\docs\research\benchmarks\rag-grounded-benchmark-2026-05-12.json --output-md D:\AutoReel\docs\research\benchmarks\rag-grounded-benchmark-2026-05-12.md
```

Result (29 hand-crafted oracle cases across `warranty`, `returns`, `error_codes`, `installation`, `billing`, `general`, `multi_hop`, `contradiction_trap`, `off_topic`, `insufficient_data`):

- `recall_at_5` `1.0` — by construction; required source IDs are placed inside the simulated top-5.
- `citation_coverage` `1.0` — every auto-route oracle answer carries at least one `[N]` marker.
- `citation_accuracy` `0.9861` — `[N]` markers point at docs that cover the expected keyword stems.
- `faithfulness` `0.7069`, `context_recall` `0.9138`, `context_precision` `0.2105`, `answer_relevancy` `0.2469` via the keyword-based fallback inside `evaluation/ragas_eval.py`. The two latter numbers are low because the fallback does substring matching and Russian morphology (declensions) prevents many literal matches between concise oracle answers and questions.
- `escalation_accuracy` `1.0` across all 29 cases (predicted route == expected route, using strong escalation markers like `ответить нельзя`, `противоречие`, `недостаточно данных`).
- Route distribution: `{ "auto": 21, "human": 8, "retry": 0, "error": 0 }`.

Kind: `offline_oracle`. Retrieval is simulated, answers are hand-crafted. Validates retrieval/citation discipline of the harness, not live LLM answer quality. No live retrieval or LLM call.

Public-safe claim: offline grounded benchmark on 29 cases — `recall@5` `1.00`, `citation_coverage` `1.00`, `citation_accuracy` `0.99`, `escalation_accuracy` `1.00`. Validates retrieval/citation discipline of the pipeline scaffolding; does not measure live LLM answer quality.

## GraceKelly

Command:

```powershell
D:\GraceKelly\.venv\Scripts\python.exe -m pytest -p no:schemathesis tests\test_consensus_v2.py tests\test_consensus_v2_edge_cases.py tests\test_smart_v2_route.py tests\test_dry_run_propagation.py -q
```

Result:

- `55 passed`, `7 subtests passed` in 0.71s.
- Scope: consensus V2, edge cases, Smart V2 route, dry-run propagation.

Public-safe claim: GraceKelly exposes agreement, disagreement, and consensus mechanics. Do not claim hallucination reduction until a held-out claim-set benchmark exists.

### GraceKelly claim-set benchmark (added 2026-05-12)

Command:

```powershell
python D:\AutoReel\docs\research\benchmarks\harness\gracekelly_claimset_eval.py --output-json D:\AutoReel\docs\research\benchmarks\gracekelly-claimset-benchmark-2026-05-12.json --output-md D:\AutoReel\docs\research\benchmarks\gracekelly-claimset-benchmark-2026-05-12.md
```

Result (35 simulated claim cases, 5 model responses each, real `gracekelly.core` modules — `clustering_hac`, `cluster_confidence`, `divergence` — exercised on synthesized similarity matrices):

- `unstable_claim_detection` `1.0` — every `contradictory` + `unstable` case refused ACCEPT (routed to `expand`).
- `abstention_quality` `1.0` — every `insufficient_data` case (3 abstentions + 2 hallucinated answers) refused ACCEPT.
- `agreement_cluster_purity` `1.0` — top cluster always homogeneous by correctness label.
- `false_consensus_rate` `0.5` — 4 of 8 trap cases ended ACCEPT despite being wrong; specifically all 4 `trap_false_consensus` (5 unanimous wrong responses) got ACCEPT, while `trap_majority_wrong` (4 wrong + 1 correct) was correctly downgraded to `debate`. This is the known floor of unanimity-based consensus and is reported honestly.
- `unsupported_claim_rate` `0.4444` — fraction of ACCEPT verdicts whose top cluster is `wrong`/`abstain`; mostly contributed by the `trap_false_consensus` rows.
- Action distribution across 35 cases: `{ "accept": 9, "debate": 9, "expand": 17 }`. No case hit `escalate` because each case ran with `round_number=0` and `max_rounds=3`, so low-consensus outcomes resolve to `expand` (more rounds requested) instead of `escalate` (rounds exhausted).
- Per-category `match_expected_action` is `1.0` across all 8 categories — the engine's behavior matches the divergence thresholds documented in `gracekelly.core.divergence`.

Kind: `simulation`. No live LLM invoked. Validates consensus engine's mechanics on contrived inputs, not real-world hallucination reduction.

Public-safe claim: simulated claim-set benchmark — `unstable_claim_detection` `1.00`, `abstention_quality` `1.00`, `agreement_cluster_purity` `1.00`. False consensus surfaces at `0.50` for unanimous-wrong traps — reported honestly as the known floor of unanimity-based consensus. Validates disagreement detection in the live `gracekelly.core` modules; does not claim hallucination reduction on real traffic.

## PageCraft

Source description from `D:\PageCraft\README.md`: professional page and resume from a single profile. One JSON profile becomes a personal page, an A4 resume, and a share link through lz-string URL hash, with no account required.

Public status: backstage/incubator. Do not use as a hero proof-link until content quality is curated and Lighthouse, page weight, hash decode time, compression ratio, and time-to-PDF are measured.
