# AgentFlow Benchmark Report

> This file is overwritten by the latest long-running benchmark. For entity-specific optimization evidence, see `docs/perf/entity-benchmark-contract.md`.

Generated: `2026-05-12T03:47:43+03:00`

## System Under Test

- OS: `Windows-11-10.0.26200-SP0`
- CPU: `Intel64 Family 6 Model 170 Stepping 4, GenuineIntel` (18 logical cores)
- RAM: `15.5 GB`
- Python: `3.13.7`

## Test Parameters

- Host: `http://127.0.0.1:8001`
- Seed step: `python -m src.processing.local_pipeline --burst 500`
- Load profile: `50` users, spawn rate `10/s`, duration `60s`
- Warmup: `10s` discarded pre-run with the same Locust traffic mix to reduce cold-start noise.
- Locust file: `tests/load/locustfile.py`

## Results

| Endpoint | Requests | Failures | Failure Rate | RPS | p50 | p95 | p99 |
|----------|----------|----------|--------------|-----|-----|-----|-----|
| ALL | 506 | 0 | 0.00% | 29.71 | 14.0 ms | 97.0 ms | 280.0 ms |
| POST /v1/batch | 82 | 0 | 0.00% | 4.81 | 22.0 ms | 110.0 ms | 330.0 ms |
| GET /v1/entity/order/{id} | 98 | 0 | 0.00% | 5.75 | 14.0 ms | 210.0 ms | 330.0 ms |
| GET /v1/entity/product/{id} | 57 | 0 | 0.00% | 3.35 | 11.0 ms | 85.0 ms | 280.0 ms |
| GET /v1/entity/user/{id} | 56 | 0 | 0.00% | 3.29 | 13.0 ms | 93.0 ms | 290.0 ms |
| GET /v1/metrics/{name} | 132 | 0 | 0.00% | 7.75 | 11.0 ms | 71.0 ms | 220.0 ms |
| POST /v1/query | 81 | 0 | 0.00% | 4.76 | 16.0 ms | 170.0 ms | 270.0 ms |

## Release Gate Context

- README no longer carries a benchmark claim table, so this run is compared against the release gate instead of documentation copy.
- Measured aggregate: p50 14.0 ms, p99 280.0 ms.
- Release gate for `/v1/entity/*`: p50 < 100 ms and p99 < 500 ms.
- Slowest entity endpoint in this run: `GET /v1/entity/order/{id}` at p50 14.0 ms, p99 330.0 ms.
- Aggregate throughput was 29.71 RPS with `50` concurrent users.

## Notes

- Benchmark comparability: this run matches or exceeds the canonical baseline (50 users, spawn rate 10/s, duration 60s).
- This report is generated from a fresh DuckDB dataset on every run.
- Re-running `python scripts/run_benchmark.py` overwrites this file with new measurements.
