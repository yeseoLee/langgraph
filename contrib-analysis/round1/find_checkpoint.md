# Checkpoint candidates, round 1 (verified by reading the code; no repros were run)

Context: #9383 (3cd57c0) added the NUL check to the shared `_validate_namespace_labels` in libs/checkpoint/langgraph/store/base/__init__.py:1325. Every store entry point calls it (base/__init__.py:779,852,963,1011,1047,1129,1250,1298,1359-1364; batch.py:90-245), so there is no store gap left to fix. Dropped.

## 1. feat(checkpoint-sqlite,checkpoint): add `prune` to SqliteSaver / AsyncSqliteSaver / InMemorySaver (parity with #9290)
- Where: base `prune`/`aprune` raise NotImplementedError (checkpoint/langgraph/checkpoint/base/__init__.py:398,585). Only Postgres overrides them (postgres/__init__.py:409, postgres/aio.py:368,745). sqlite/__init__.py, sqlite/aio.py and memory/__init__.py have no override (grep of `def prune` finds none).
- Repro (not run): `SqliteSaver.from_conn_string(":memory:")` then `.prune(["t"], strategy="delete")` raises NotImplementedError. Same for InMemorySaver.
- Fix: port the semantics from #9290. `"delete"` runs the delete_thread logic across many threads. `"keep_latest"` keeps the latest checkpoint per ns plus its writes, refuses DeltaChannel threads with NotImplementedError, and raises TypeError for a single str.
- Test idea: reuse the Postgres prune tests and the conformance suite's prune capability for sqlite and memory.
- Scope note: this is a feature. A maintainer should confirm they want it before work starts. #8531 (closed) was Postgres only.
- Dup queries: "prune sqlite", "prune InMemorySaver", "prune checkpointer", "SqliteSaver prune", "prune parity" (issues and PRs). Results: only #8531 and #9290 (both Postgres) plus unrelated items. No sqlite/memory match.

## 2. fix(checkpoint-sqlite,checkpoint-postgres): `list(before=...)` with no `checkpoint_id` returns nothing (InMemorySaver returns everything)
- Where: checkpoint-sqlite/langgraph/checkpoint/sqlite/utils.py `search_where` (`if before is not None: wheres.append("checkpoint_id < ?"); param_values.append(get_checkpoint_id(before))`). checkpoint-postgres/langgraph/checkpoint/postgres/base.py:872-874 does the same. When `before={"configurable":{"thread_id":"t"}}`, the bound value is None, so `checkpoint_id < NULL` matches no rows. InMemorySaver (memory/__init__.py:369-373) skips the filter when the id is falsy and returns all rows.
- Repro (not run): put 3 checkpoints, then `list(cfg, before={"configurable":{"thread_id":"t"}})`. Memory returns 3, SQLite and Postgres return 0.
- Fix: guard with `if before is not None and (bid := get_checkpoint_id(before)):` in both search_where functions (sync and async share them).
- Test idea: parametrized saver test, or a conformance-suite case.
- Dup queries: "before checkpoint_id list", "list before sqlite", "before without checkpoint_id", "before config checkpoint_id None" (issues and PRs). Results: #7308 (closed; InMemorySaver before/limit test coverage), #8656 (open; limit=-1 divergence, a different parameter), #7204 (N+1 perf). No match.

## 3. fix(checkpoint-sqlite): `SqliteSaver.list(filter=...)` with a dict value depends on key order (InMemorySaver uses equality, Postgres uses `@>`)
- Where: checkpoint-sqlite/langgraph/checkpoint/sqlite/utils.py:55-58 compares `json_extract(metadata,'$.k') = json.dumps(value, separators=(",",":"))`. json_extract returns the object with keys in stored order, so `{"b":1,"a":2}` does not match a stored `{"a":2,"b":1}`. InMemorySaver (memory/__init__.py:380) uses `==`, which ignores order. Postgres (base.py:868) uses `metadata @> %s`, which also ignores order and allows partial matches.
- Repro (not run): put a checkpoint with metadata `{"src":{"a":1,"b":2}}`, then `list(None, filter={"src":{"b":2,"a":1}})`. Memory returns 1, SQLite returns 0.
- Fix: compare canonical forms. Either use json_extract with sort keys in SQL (no native support, so a per-key predicate on each nested path), or filter dict and list values in Python after the query.
- Test idea: sqlite test with reordered nested dict keys, also covering AsyncSqliteSaver.
- Adjacent: #9074 (open; non-ASCII nested values) touches the same line with a different cause. Mention it and coordinate, possibly in one PR.
- Dup queries: "sqlite metadata filter nested", "json_extract filter", "filter dict metadata sqlite", "key order filter sqlite", "nested dict filter SqliteSaver" (issues and PRs). Results: #9074 and PR #9083 (Unicode, closed), #8786/#9266 (Store, not Saver), #8759/#8814 (Store BLOB). No key-order match.

Also seen: `_where_value`'s bool branch (utils.py:53) is unreachable because bool is an int subclass. The behavior still works because SQLite binds True as 1, so this is not a candidate.
