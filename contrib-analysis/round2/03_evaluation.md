# LangGraph Round 2 evaluation (M / T / D / I / P -> average; pass >= 5.5)

| ID | M | T | D | I | P | Avg | Result |
|---|---|---|---|---|---|---|---|
| L2-1 compose env YAML escaping | 7 | 7 | 9 | 7 | 8 | 7.6 | **Pass** -> merged with L2-2 |
| L2-2 Dockerfile ENV single-quote | 7 | 7 | 9 | 6 | 7 | 7.2 | **Pass** (merged, 10 places in config.py:1266-1294) |
| L2-3 --add-docker-compose env_file hardcoded | 6 | 6 | 8 | 6 | 6 | 6.4 | **Pass** (check closed PR #9063) |
| L2-4 threads.search(ids=[]) | 6 | 5 | 9 | 8 | 8 | 7.2 | **Pass** -> merged with L2-6 (truthiness checks) |
| L2-5 crons.count enabled | 4 | 3 | 8 | 4 | 3 | 4.4 | Reject (feature, server support unverified) |
| L2-6 assistants.update(description="") | 5 | 5 | 9 | 5 | 5 | 5.8 | **Pass** (merged) |
| L2-7 RemoteGraph drops None | 4 | 3 | 8 | 5 | 6 | 5.2 | Reject (little effect given server-side `.get()`) |
| L2-8 SqliteStore datetime binding | 6 | 6 | 7 | 6 | 6 | 6.2 | **Pass** (scope to the py3.12 deprecation; drop the sweep_ttl claim) |
| L2-9 InMemoryStore empty namespace | 3 | 4 | 3 | 4 | 3 | 3.4 | Reject (expand scope as a comment on #8734) |
| L2-10 SqliteStore omit_expired | 3 | 4 | 1 | 6 | 1 | 3.0 | Reject (#8902 closed not planned by maintainer) |
