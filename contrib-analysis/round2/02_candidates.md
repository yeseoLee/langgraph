# LangGraph Round 2 candidates (consolidated)

- Sources: `find_cli.md`, `find_sdk.md`, `find_core.md`, `find_store.md`.
- Duplicate search was done during discovery (some queries hit the rate limit; see each file). Nothing was executed.
- Needed: 6 more passes (4/10 so far).

| ID | Title (draft) | Type | Notes |
|---|---|---|---|
| L2-1 | fix(cli): env dict values not escaped in generated docker-compose YAML (`f'{k}: "{v}"'`) | bug | |
| L2-2 | fix(cli): single quote in config JSON breaks Dockerfile `ENV X='...'` | bug | |
| L2-3 | fix(cli): `dockerfile --add-docker-compose` hardcodes `env_file: [.env]`, ignores config `env` | bug | |
| L2-4 | fix(sdk-py): `threads.search(ids=[])` returns all threads (truthiness check) | bug | |
| L2-5 | fix(sdk-py): `crons.count` lacks `enabled` filter (parity with `crons.search`) | bug/feature | server support unverified |
| L2-6 | fix(sdk-py): `assistants.update(description="")` silently dropped | bug | server behavior unverified |
| L2-7 | fix(langgraph): `RemoteGraph` config sanitization drops `None` values and shifts list items | bug | some duplicate queries cut off by the rate limit |
| L2-8 | fix(checkpoint-sqlite): `SqliteStore` binds `expires_at` as raw `datetime` (py3.12 deprecated adapter; mixed formats vs `sweep_ttl`) | bug | |
| L2-9 | fix(checkpoint): `InMemoryStore` delete leaves an empty namespace in `list_namespaces()` | bug | near-duplicate of #8734 (ghost namespaces) |
| L2-10 | fix(checkpoint-sqlite): `SqliteStore` ignores `TTLConfig.omit_expired` | bug | #8902 closed, PRs #8903–8905 closed (re-raise?) |
