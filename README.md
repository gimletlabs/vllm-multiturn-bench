# vllm-multiturn-bench (Gimlet fork)

Gimlet fork of vLLM's `benchmarks/multi_turn/` for use as the multi-turn benchmark
runner against Gimlet OpenAI-compatible chat-completions endpoints.

## Vendoring posture

This repo carries a pinned snapshot of `benchmarks/multi_turn/` from
[vllm-project/vllm](https://github.com/vllm-project/vllm) plus two small Gimlet-side
patches. Cloned at Docker build time by the in-tree
`src/experimental/benchmark_sweep/docker/multi_turn.Dockerfile`, mirroring how
`gimletlabs/bench_serving` is operated.

**Upstream pin:** `efb4cdf2b8000c850d04706eb6f788903e3ee544` (vllm-project/vllm@main, fetched 2026-04-30).

## Patches on top of upstream

1. **Auth headers** — adds `--auth-token` and `--api-key` CLI flags to
   `benchmark_serving_multi_turn.py` and injects `Authorization: Bearer` and
   `X-API-KEY` headers on the chat-completions POST when set. Required to talk
   to Gimlet endpoints with auth enforced.
2. **Per-turn-keyed aggregation** — adds `per_turn_breakdown` to the
   `--stats-json-output` JSON, grouped by `input_num_turns`. Un-excludes
   `approx_cached_percent` from the summary so the prefix-cache hit signal
   flows through. Note: this changes the output shape from a flat list to a
   dict (`{"per_request": [...], "per_turn_breakdown": {...}}`).

## Upstream contribution

Both patches are written to be vanilla and PR-able to upstream vLLM. If
accepted upstream, this fork can be retired in favor of a pinned upstream
release.

## Rebasing against upstream

To pick up upstream changes, rebase the patch commits onto a newer upstream
SHA. The patches are small and touch isolated parts of the file (argparse,
headers dict, aggregation block); conflicts should be rare.

## License

Apache-2.0, inherited from upstream vLLM. See `benchmarks/multi_turn/`'s
SPDX headers.
