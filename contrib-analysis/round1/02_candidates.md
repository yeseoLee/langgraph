# LangGraph Round 1 candidates (consolidated)

- Sources: `find_core.md`, `find_sdk.md`, `find_checkpoint.md`, `find_cli_docs.md`.
- Duplicate search was done during discovery. Nothing was executed.

| ID | Title (draft) | Type | Notes |
|---|---|---|---|
| L1-1 | fix(langgraph): `StateGraph.add_edge([], "b")` accepts empty start list | bug | `add_sequence` has the same check |
| L1-2 | fix(langgraph): `add_conditional_edges` should reject `END` as source early (parity with `add_edge`) | bug/UX | currently fails at compile time |
| L1-3 | fix(langgraph): `EphemeralValue(guard=True)` error lacks error code (parity with `LastValue`) | UX | |
| L1-4 | docs(sdk-py): async `threads.join_stream` example missing `async for`/`await`; `stream_mode` undocumented | docs | |
| L1-5 | docs(sdk-py): sync `runs.join_stream` docstring says returns `None`; example never iterates | docs | |
| L1-6 | docs(sdk-py): `runs.join` docstring says returns `None` (both clients) | docs | #5442 (2025) supposedly fixed this already — regression? check history |
| L1-7 | feat(checkpoint-sqlite,checkpoint): `prune` for Sqlite/InMemory savers (parity with #9290) | feature | needs maintainer intent first |
| L1-8 | fix(checkpoint-sqlite,checkpoint-postgres): `list(before=...)` without `checkpoint_id` returns nothing (InMemory returns all) | bug | |
| L1-9 | fix(checkpoint-sqlite): `list(filter=...)` dict values depend on key order | bug | related #9074/#9083 (non-ASCII, same line) |
| L1-10 | feat(cli): `--json` for `deploy revisions list` (parity with #9395, #9243) | feature | |
| L1-11 | feat(cli): JSON Lines output for `deploy logs` | feature | |
| L1-12 | test(cli): cover `langgraph validate` and `get_unknown_keys` branches | test | |
