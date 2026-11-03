# LangGraph Round 1 evaluation (M / T / D / I / P -> average; pass >= 5.5)

| ID | M | T | D | I | P | Avg | Result |
|---|---|---|---|---|---|---|---|
| L1-1 add_edge empty start list | 4 | 5 | 8 | 5 | 7 | 5.8 | **Pass** (silently never fires) |
| L1-2 add_conditional_edges END source | 3 | 3 | 7 | 6 | 6 | 5.0 | Reject (already caught at compile time) |
| L1-3 EphemeralValue error code | 2 | 3 | 9 | 4 | 4 | 4.4 | Reject (cosmetic) |
| L1-4 async threads.join_stream example | 7 | 8 | 8 | 7 | 7 | 7.4 | **Pass** -> merged with L1-5/L1-6 as one docs PR |
| L1-5 sync runs.join_stream docstring | 7 | 8 | 8 | 7 | 7 | 7.4 | **Pass** (merged) |
| L1-6 runs.join docstring | 6 | 6 | 4 | 5 | 6 | 5.4 | Merged into the docs PR, citing #5442 |
| L1-7 prune parity | 3 | 4 | 8 | 6 | 3 | 4.8 | Reject (feature, needs approval) |
| L1-8 list(before) without checkpoint_id | 6 | 7 | 7 | 7 | 8 | 7.0 | **Pass** |
| L1-9 sqlite filter key order | 5 | 6 | 4 | 6 | 5 | 5.2 | Reject (#8787, #9074 overlap) |
| L1-10 deploy revisions list --json | 7 | 6 | 8 | 6 | 5 | 6.4 | **Pass** (follows the --json series) |
| L1-11 deploy logs JSONL | 3 | 4 | 8 | 5 | 3 | 4.6 | Reject |
| L1-12 cli validate tests | 4 | 5 | 7 | 5 | 4 | 5.0 | Reject |
