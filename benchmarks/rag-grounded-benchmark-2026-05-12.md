# RAG offline grounded benchmark — 2026-05-12

> Kind: `offline_oracle`. Retrieval is simulated, answers are hand-crafted with explicit `[N]` citations referencing the provided `retrieved_docs`.
> This validates retrieval/citation discipline of the harness, not live LLM answer quality.

- Cases: `29`
- Source repo: `D:\RAG_Support_Assistant`
- Generated: `2026-05-12T05:06:20`

## Aggregate

| metric | value |
|--------|------:|
| `recall_at_5` | 1.0 |
| `citation_coverage` | 1.0 |
| `citation_accuracy` | 0.9861 |
| `faithfulness` | 0.7069 |
| `answer_relevancy` | 0.2469 |
| `context_precision` | 0.2105 |
| `context_recall` | 0.9138 |
| `escalation_accuracy` | 1.0 |

Route distribution (predicted vs. expected): {"auto": 21, "human": 8, "retry": 0, "error": 0}

## Per-category

| category | n | recall@5 | cite_cov | cite_acc | faithfulness | relevancy | escal_acc |
|----------|--:|--------:|--------:|--------:|-------------:|----------:|----------:|
| `billing` | 2 | 1.0 | 1.0 | 1.0 | 1.0 | 0.1666 | 1.0 |
| `contradiction_trap` | 3 | None | None | 1.0 | 0.1667 | 0.2167 | 1.0 |
| `error_codes` | 5 | 1.0 | 1.0 | 1.0 | 1.0 | 0.4033 | 1.0 |
| `general` | 1 | 1.0 | 1.0 | 1.0 | 1.0 | 0.0 | 1.0 |
| `installation` | 2 | 1.0 | 1.0 | 1.0 | 1.0 | 0.125 | 1.0 |
| `insufficient_data` | 1 | None | None | None | 0.0 | 0.4 | 1.0 |
| `multi_hop` | 2 | 1.0 | 1.0 | 0.8334 | 0.5 | 0.4921 | 1.0 |
| `off_topic` | 4 | None | None | None | 0.0 | 0.0 | 1.0 |
| `returns` | 4 | 1.0 | 1.0 | 1.0 | 1.0 | 0.4188 | 1.0 |
| `warranty` | 5 | 1.0 | 1.0 | 1.0 | 1.0 | 0.17 | 1.0 |

## Caveats

- This is **not** a live RAG quality benchmark. No retrieval was executed and no LLM was called.
- `faithfulness` and `answer_relevancy` use the keyword-based fallback inside `evaluation/ragas_eval.py`; semantic embeddings are disabled.
- `escalation_accuracy` reflects whether the harness picks up Russian escalation phrasing (`ответить нельзя`, `ответить однозначно нельзя`, `однозначного ответа`, `недостаточно данных`, `противоречие`, `не покрыто`). Plain `обратитесь в поддержку` is treated as legitimate action advice, not escalation.
- Low `answer_relevancy` and `context_precision` reflect the keyword-overlap nature of the fallback metric in `evaluation/ragas_eval.py`: Russian morphology (declensions) reduces substring matches between concise oracle answers and questions. Semantic answer relevancy needs a live embedding model.
- Route distribution covers `auto`/`human`; `retry`/`error` are reported as `0` because no live agent run is involved.