# Rebuttal 4: user impact & fix risk
Score 10 = high impact with a low-risk fix.

- L2-1: 7. Quotes and backslashes in `env` values are realistic (passwords, Windows paths). Invalid YAML fails loudly, but `\t` and similar escapes corrupt values SILENTLY, which is worse. The json.dumps fix is tiny and well-contained. Risk: values that rely on the current interpolation, such as `${VAR}`, must keep working (JSON quoting preserves `$`).
- L2-2: 6. An apostrophe in a graph description or an auth/http string breaks `langgraph build`. It fails loudly, so users notice and work around it. The fix touches many ENV lines that every deployment uses, so it has wide blast radius and needs careful shell-quoting tests.
- L2-3: 6. Docker compose fails when `.env` is missing, or silently loses dict env. The `dockerfile --add-docker-compose` path is less used than `up`. Low-risk fix, but it changes generated output (snapshot tests).
- L2-4: 8. `ids=[]` returning ALL threads is a correctness and data-exposure issue in multi-tenant apps. The one-line fix is `is not None`. Risk: callers who pass `[]` and expect "no filter", which is unlikely.
- L2-5: 4. Parity gap only, and it is additive. Server support is unverified, and if the server rejects `enabled` the fix is useless. Low risk if it is keyword-only.
- L2-6: 5. Users can't clear description/name. The data is stale but harmless. Server handling of "" is unverified (it might 422 on name=""), so limit the change to description, or verify first.
- L2-7: 5. Dropping `None` changes semantics only for keys whose meaning is "explicit null". List shifting is rare in config. The sentinel refactor is small, but behavior changes for every RemoteGraph call. Medium risk.
- L2-8: 6. The DeprecationWarning breaks `-W error` suites on py3.12+, and mixed formats can mis-sweep TTL (text comparison of "...+00:00" vs "..."). It changes the stored format, so it needs migration thinking for existing rows (old rows keep the tz suffix). It also overlaps #8806/#9559 edits.
- L2-9: 4. Cosmetic leak in a dev/test store, plus minor memory growth. Trivial, safe fix.
- L2-10: 6. Expired rows are served from SqliteStore, a real correctness issue, but a maintainer declined it. The fix already exists in closed PRs. Impact counts for nothing if the work can't land.

| ID | score | rating |
|---|---|---|
| L2-1 | 7 | high, safe |
| L2-2 | 6 | loud failure, wide edit |
| L2-3 | 6 | moderate |
| L2-4 | 8 | highest |
| L2-5 | 4 | low, unverified |
| L2-6 | 5 | moderate, verify server |
| L2-7 | 5 | niche, medium risk |
| L2-8 | 6 | real, format migration |
| L2-9 | 4 | low |
| L2-10 | 6 | real but declined |
