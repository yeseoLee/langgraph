# LangGraph: contribution points that passed the rebuttal review: final 10

- Criterion: 5-perspective rebuttal (maintainer, technical, duplicates, impact, process), average >= 5.5, no fatal rebuttal.
- Duplicate check: GitHub search API (keyword-based). Re-check right before filing.
- Workflow: file the issue (bug template) -> get assigned -> PR (`Fixes #N`, `type(scope):`, 1-2 sentence body). There is no feature template, so for feature items ask a maintainer first.
- Nothing was executed. Reproduce locally before filing.
- Items whose rebuttals recommended combining them into one PR are counted as one.

| Rank | Round | Title (draft) | Avg | Type | Conditions / caveats |
|---|---|---|---|---|---|
| 1 | R2 | fix(cli): escape config values in generated Dockerfile `ENV` and compose YAML | 7.4 | bug | Single quotes (10 places in config.py) / backslashes / double quotes |
| 2 | R1 | docs(sdk-py): fix `join_stream`/`join` docstrings and examples | 7.4 | docs | async iteration, return types, `stream_mode`. Cite #5442 |
| 3 | R1 | fix(checkpoint-sqlite,checkpoint-postgres): `list(before=...)` without `checkpoint_id` returns nothing | 7.0 | bug | InMemorySaver differs. Watch for conflicts with open PR #7204 |
| 4 | R2 | fix(sdk-py): empty values (`ids=[]`, `description=""`) dropped by truthiness checks | 6.5 | bug | Confirm server semantics |
| 5 | R1 | feat(cli): `--json` for `deploy revisions list` | 6.4 | feature (parity) | Follows #9395/#9243. Ask a maintainer first |
| 6 | R2 | fix(cli): `dockerfile --add-docker-compose` honors config `env` | 6.4 | bug | Check closed PR #9063 |
| 7 | R2 | fix(checkpoint-sqlite): `SqliteStore` binds `expires_at` with the deprecated datetime adapter | 6.2 | bug | py3.12+ warning only (drop the sweep_ttl claim). #8806, #9558 |
| 8 | R4 | fix(checkpoint): `EncryptedSerializer` validates explicit key length and replaces `assert` | 6.2 | bug | Coordinate with #9470 |
| 9 | R1 | fix(langgraph): `StateGraph.add_edge([], node)` should raise | 5.8 | bug | Same check as `add_sequence` |
| 10 | R3 | fix(cli): `langgraph new` handles network errors, bad archives, and file paths | 5.6 | bug | Lead with URLError/BadZipFile; find out why #8075/#8111 were closed |

## Main rejection reasons
- Already closed by a maintainer: SqliteStore omit_expired (#8902 not planned), sqlite from_conn_string serde (Postgres version declined 3 times)
- Existing issue already covers it: InMemoryStore empty namespace (#8734), interrupt_before tuple (#9277), sqlite filter key order (#8787/#9074)
- Not a bug / low impact: add_conditional_edges END (caught at compile), RemoteGraph None, Interrupt(ns=str), EphemeralValue error code
- Feature needing approval: prune parity, deploy logs JSONL, crons.count enabled

## Strategy notes
- Maintainers work in "series" on the core runtime (DeltaChannel, streaming, resume), so this list leans toward cli / sdk-py / checkpoint.
- File in order 1 -> 3 -> 2. One at a time.
