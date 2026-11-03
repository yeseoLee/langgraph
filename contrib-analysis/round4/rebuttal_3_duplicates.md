# Rebuttal 3: duplicates (re-searched 2026-11-03, sleep 3 between queries)
- R4-1: `is:issue interrupt_before tuple` -> 8629, 7127, 6486, 4973, 6104 (unrelated). `is:pull-request` -> 8637, 8548 (unrelated). `is:issue interrupt_before string characters` -> **#9277 open** "interrupt_after="node_one" pauses on a node named "no"". The issue page itself could not be fetched (gh issues API returned 403 for this repo), so the relation is judged from the title only: same normalization code (state.py:1257-1265) and the same root cause (the arg is not normalized to a list). A fix for #9277 such as `list(x)` or a str check would likely also fix tuples, or come close. That makes R4-1 a near-duplicate in scope: comment on #9277 rather than file a new issue.
- R4-2: `is:issue EncryptedSerializer key length` -> 9470 (mac_len), 9004 (AAD), both open, neither about key length/assert. No PRs. Not a duplicate, but the same function is busy (also 8938).
- R4-3: `is:issue SqliteSaver serde` -> 9480, 8673, 9004, 9086, 8938 (none about sqlite from_conn_string). PRs -> 7811, 7547, 3852, 1168 (unrelated). Not a direct duplicate, but the Postgres twins 8116/9118 and PRs 8117/8233/9162 were all closed. This is precedent, not a dup.

| ID | score | verdict |
|---|---|---|
| R4-1 | 3 | overlaps #9277; fold into it |
| R4-2 | 8 | novel |
| R4-3 | 6 | novel but rejected precedent |
