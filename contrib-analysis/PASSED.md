# LangGraph: contribution points that passed the rebuttal review (cumulative, goal 10)

- Criterion: 5-perspective rebuttal, average >= 5.5, no fatal rebuttal.
- Duplicate check via GitHub search API. Nothing was executed.

| # | Round | Title (draft) | Avg | Conditions / caveats |
|---|---|---|---|---|
| 1 | R1 | fix(checkpoint-sqlite,checkpoint-postgres): `list(before=...)` without `checkpoint_id` returns nothing | 7.0 | InMemorySaver ignores the filter. Watch for conflicts with open PR #7204 |
| 2 | R1 | docs(sdk-py): fix `join_stream`/`join` docstrings and examples (async iteration, return types, `stream_mode`) | 7.4 | L1-4/5/6 combined; cite #5442 |
| 3 | R1 | feat(cli): `--json` for `deploy revisions list` | 6.4 | Parity with #9395, #9243. No feature template, so explain parity in the bug template or ask a maintainer |
| 4 | R1 | fix(langgraph): `StateGraph.add_edge([], node)` should raise | 5.8 | Same check as `add_sequence` |
