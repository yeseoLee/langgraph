# Round 2: Store/saver candidates (nothing executed)

## S2-1 fix(checkpoint-sqlite): `SqliteStore` binds raw `datetime` for `expires_at` (Python 3.12+ default-adapter DeprecationWarning, mixed timestamp formats)
- Where: libs/checkpoint-sqlite/langgraph/store/sqlite/base.py:383-397 (`now = datetime.now(timezone.utc)`; `expires_at = now + timedelta(...)` bound as a param). No `register_adapter` anywhere in the package. AsyncSqliteStore reuses the same prepare code.
- Problem: Python 3.12 deprecated sqlite3's default datetime adapter, so `put(..., ttl=N)` emits a DeprecationWarning (and raises under `-W error`, which many test suites use). The stored value is `"YYYY-MM-DD HH:MM:SS.ffffff+00:00"`. The refresh path (line ~326, `DATETIME(CURRENT_TIMESTAMP, ...)`) writes `"YYYY-MM-DD HH:MM:SS"`, so a column ends up holding two formats that `sweep_ttl` compares as text against `CURRENT_TIMESTAMP` (line ~1141).
- Repro (not run): `python -W error::DeprecationWarning -c` + `SqliteStore.from_conn_string(":memory:")` with ttl config, `setup()`, `put(("a",),"k",{"x":1},ttl=1)` -> DeprecationWarning raised.
- Fix: bind `expires_at.strftime("%Y-%m-%d %H:%M:%S")` (UTC, no tz), or compute it in SQL with `DATETIME(CURRENT_TIMESTAMP, '+' || ? || ' minutes')`, so both paths use the same format.
- Test: run the put with `warnings.simplefilter("error")`; check that the stored `expires_at` matches `^\d{4}-\d\d-\d\d \d\d:\d\d:\d\d$`.
- Dup queries: "default+datetime+adapter" (issue: none relevant; PR: only dependabot #7973), "sqlite+datetime+adapter+deprecated" (none / #7973), "SqliteStore+python+3.12+DeprecationWarning" (none/none). SURVIVES.

## S2-2 fix(checkpoint): `InMemoryStore` delete leaves an empty namespace that `list_namespaces()` still returns
- Where: libs/checkpoint/langgraph/store/memory/__init__.py:404-408 (`_apply_put_ops` pops the key but never drops an empty `self._data[namespace]` or `_vectors[namespace]`); list path :460-476 lists `self._data.keys()`.
- Parity: SQLite/Postgres list namespaces with `SELECT DISTINCT prefix FROM store`, so a namespace disappears once its last row is deleted.
- Repro (not run): `s=InMemoryStore(); s.put(("a",),"k",{"v":1}); s.delete(("a",),"k"); s.list_namespaces()` -> `[("a",)]` (SQL stores return `[]`).
- Fix: after the pop, `if not self._data[namespace]: del self._data[namespace]` (same for `_vectors`).
- Test: put+delete, then assert `list_namespaces() == []`. Add it to checkpoint-conformance if store tests live there.
- Dup queries: "InMemoryStore+delete+list_namespaces", "list_namespaces+after+delete+empty", "InMemoryStore+list_namespaces+deleted", "InMemoryStore+delete+namespace+remains". Only #8734 (open: *reads* create namespaces) and its PR #8735 (closed) came up. Same symptom, different path (writes/deletes). Best filed as a comment on #8734 or a sibling issue that links it. NEAR-DUP: needs a judgment call.

## S2-3 (lead, dup-adjacent) `SqliteStore` still ignores `TTLConfig.omit_expired`
- `grep omit_expired libs/checkpoint-sqlite` returns nothing. Postgres has `_omit_expired` (postgres/base.py ~612). Issue #8902 was CLOSED, and fix PRs #8903/#8904/#8905 are all closed. They look unmerged in this tree. Check upstream master before acting. This is a possible "reopen" and not a new issue.

## Dropped as duplicates
- Postgres $gt/$lt compare as text: #7684 open (many closed PRs).
- InMemory $gt crashes on missing/non-numeric value (float(None)): #7880 open, #8365.
- InMemory $ne includes missing fields: #8366 open.
- created_at reset on upsert (InMemory, Sqlite): #7411, #8340, #9331.
- $and/$or, nested dict filters, negative limit/offset: #8829, #9266, #8696.
