# Proof Pack

Single-page proof-of-work portfolio for four data/AI products. Every number on the page is labelled by how it was measured:

- `live · product` — real artifact from a working product.
- `offline · oracle` — hand-crafted cases with reference answers; validates pipeline discipline, not live model quality.
- `simulation` — synthetic inputs running through live core modules; validates mechanism, not production effect.

## Layout

- `index.html` — the page.
- `benchmarks/` — raw JSON + MD artifacts referenced from the page.

## Projects

1. **AB Test Research Designer** — pre-launch experiment-design layer; `57%` false-positive risk caught before launch.
2. **DE_project / AgentFlow** — real-time entity context for AI agents; aggregate `p50 14ms`, `p99 280ms`, `0` failures (2026-05-12 load benchmark).
3. **RAG Support Assistant** — production RAG with citations and confidence gates; 29-case offline grounded oracle: `recall@5 1.00`, `citation_accuracy 0.99`, `escalation_accuracy 1.00`.
4. **GraceKelly** — multi-LLM consensus engine; 35-case claim-set simulation on live `gracekelly.core` modules: `unstable_claim_detection 1.00`, `abstention_quality 1.00`, `false_consensus_rate 0.50` (unanimity floor, reported honestly).

## Contact

- uedomskikh@gmail.com
- Telegram @JuliaPhe
- +7 967 208-01-43
