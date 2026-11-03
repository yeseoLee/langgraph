# Rebuttal 1: strict LangGraph maintainer view

Question: would I assign this to an external contributor? Recent series in history (origin/main): sdk-py stream rejoin (#9554), CLI deploy `--json` (#9395, #9243), Postgres prune (#9290), store NUL checks (#9383).

- L1-1: weak accept, 4/10. Real but niche. `add_edge([], "b")` passes the list loop at libs/langgraph/langgraph/graph/state.py:969-978 and `add_sequence` already rejects empty input (state.py:1056), so a parity fix is a one-liner. Low value; it might get merged as a drive-by.
- L1-2: reject, 3/10. It already fails in `validate`/compile (state.py:1138-1140 rejects unknown sources). This only moves the error earlier. It is UX churn on a core API with no reported user pain.
- L1-3: reject, 2/10. It is cosmetic. The message at libs/langgraph/langgraph/channels/ephemeral_value.py:63-64 is already clear. Adding an error code needs a new `ErrorCode` entry plus a docs page, and maintainers own that.
- L1-4: accept, 7/10. The example is a real copy-paste failure at libs/sdk-py/langgraph_sdk/_async/threads.py:809 (`for` over a coroutine), and `stream_mode` is undocumented. Docstring fixes in the SDK are welcome, and #9554 shows the area is active.
- L1-5: accept, 7/10. libs/sdk-py/langgraph_sdk/_sync/runs.py:1124-1125 says `Returns: None` although the method returns an Iterator, and the example never iterates. Bundle it with L1-4 and L1-6 into one sdk-py docs PR. Three separate PRs would be rejected as spam.
- L1-6: accept (bundle), 6/10. Both `join` docstrings say `None` against `-> dict` (_sync/runs.py:1077-1078; _async/runs.py:1086-1087, which also has the `result =await` typo). Fold it into the L1-4/5 PR.
- L1-7: reject unless asked, 3/10. This is a feature and a public API surface on two savers. The Postgres prune (#9290) was done by the team. Contributors should open an issue for intent first, and I would not assign it.
- L1-8: accept, 6/10. It is a behavioral divergence between savers. libs/checkpoint-sqlite/langgraph/checkpoint/sqlite/utils.py:113-115 and libs/checkpoint-postgres/langgraph/checkpoint/postgres/base.py:872-874 differ from memory/__init__.py:369-373. Small bug fix with an obvious test, so it is welcome.
- L1-9: maybe, 5/10. The sqlite filter is sensitive to key order (utils.py:57-58). It is a real bug but an edge case, and it overlaps #9074/#9083 on the same lines. Check those first or risk a duplicate.
- L1-10: accept, 7/10. It follows the active `--json` series directly (#9395, #9243). `deploy_revisions_list` at libs/cli/langgraph_cli/deploy.py:2694-2724 is the last gap, so it is a natural pattern-match PR.
- L1-11: reject for now, 3/10. A new output format for `deploy logs` together with `--follow` streaming is a design decision (JSONL schema). The team should decide it, so the contributor needs an issue first.
- L1-12: weak accept, 4/10. Test-only PRs for libs/cli/langgraph_cli/cli.py:872-905 are fine but low priority. Reviewers often deprioritize them.

| ID | score | verdict |
|---|---|---|
| L1-1 | 4 | weak accept |
| L1-2 | 3 | reject |
| L1-3 | 2 | reject |
| L1-4 | 7 | accept (bundle) |
| L1-5 | 7 | accept (bundle) |
| L1-6 | 6 | accept (bundle) |
| L1-7 | 3 | reject w/o intent |
| L1-8 | 6 | accept |
| L1-9 | 5 | maybe (dup check) |
| L1-10 | 7 | accept |
| L1-11 | 3 | reject for now |
| L1-12 | 4 | weak accept |
