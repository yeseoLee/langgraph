# LangGraph: contribution points that passed the rebuttal review (cumulative, goal 10)

- Criterion: 5-perspective rebuttal, average >= 5.5, no fatal rebuttal.
- Duplicate check via GitHub search API. Nothing was executed.

| # | Round | Title (draft) | Avg | Conditions / caveats |
|---|---|---|---|---|
| 1 | R1 | fix(checkpoint-sqlite,checkpoint-postgres): `list(before=...)` without `checkpoint_id` returns nothing | 7.0 | InMemorySaver ignores the filter. Watch for conflicts with open PR #7204 |
| 2 | R1 | docs(sdk-py): fix `join_stream`/`join` docstrings and examples (async iteration, return types, `stream_mode`) | 7.4 | L1-4/5/6 combined; cite #5442 |
| 3 | R1 | feat(cli): `--json` for `deploy revisions list` | 6.4 | Parity with #9395, #9243. No feature template, so explain parity in the bug template or ask a maintainer |
| 4 | R1 | fix(langgraph): `StateGraph.add_edge([], node)` should raise | 5.8 | Same check as `add_sequence` |
| 5 | R2 | fix(cli): escape config values in generated Dockerfile `ENV` and compose YAML | 7.4 | L2-1+L2-2 combined. Single quotes / backslashes / double quotes |
| 6 | R2 | fix(sdk-py): empty values (`ids=[]`, `description=""`) dropped by truthiness checks | 6.5 | L2-4+L2-6 combined. Check server semantics (empty ids -> return empty without a request) |
| 7 | R2 | fix(cli): `dockerfile --add-docker-compose` honors config `env` | 6.4 | Check against closed PR #9063 |
| 8 | R2 | fix(checkpoint-sqlite): `SqliteStore` binds `expires_at` with the deprecated datetime adapter | 6.2 | Python 3.12+ DeprecationWarning. Watch for conflicts with #8806, #9558 |
| 9 | R3 | fix(cli): `langgraph new` handles network errors, bad archives, and file paths | 5.6 | L3-1+L3-2 combined. Find out why #8075/#8111 were closed |
