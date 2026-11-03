# Rebuttal 3: duplicate re-search (keywords differ from discovery)
Method: `gh api search/issues` with is:issue / is:pull-request. Direct `repos/.../issues/N` calls return 403 in this session, so the specific numbers were checked through search only. #7408 never showed up in any search result and is unverified.

Queries run: "add_edge empty list", "add_conditional_edges END" (PR), "EphemeralValue guard", "join_stream docstring" (PR), "runs.join returns" (PR), "5442", "sdk docstring examples" (PR), "sqlite prune" (PR), "before checkpoint_id sqlite" (PR), "filter sqlite nested" (PR), "deploy revisions json" (PR), "deploy logs json", "cli validate test" (PR).

Key findings
- #5442 (closed) says `runs.join` was typed `void` when it should return `ThreadState["values"]`. If the docstring still says `None`, either the fix only touched the type or the issue was closed without a fix. L1-6 is either a leftover or a regression. Not a new duplicate, but it must cite #5442.
- #8787 (closed PR) "fix(checkpoint-sqlite): support nested object equality filters" touched the same filter path as L1-9. Read its diff before filing: key-order handling may already be covered, or may have been knowingly left out. #9083 (closed, Unicode) and #9074 (open) are adjacent.
- #6926 and #7064 (closed) handle END in conditional-edge path_map lookup. That is a different problem from END as the *source* (L1-2), but it shows prior attention to END handling.
- #9318 (closed) improved errors for conditional routers. L1-2 is a related UX area.
- #7204 (open, perf N+1 in sqlite list) touches `list()`. L1-8 may conflict with it at merge time but is not a duplicate.
- No hits for prune in sqlite or memory, EphemeralValue error codes, add_edge empty list, `revisions list --json`, deploy logs JSONL, or validate tests.

## Scores (10 = clearly no duplicate)
- L1-1: 8. No hits for an empty start list.
- L1-2: 7. Neighbors are #6926, #7064 and #9318, but none covers END as source.
- L1-3: 9. No hits.
- L1-4: 8. No join_stream docstring PRs.
- L1-5: 8. Same as L1-4.
- L1-6: 4. #5442 already covered this area. Treat it as a regression or leftover and reference it.
- L1-7: 8. Only Postgres items (#8531, #9290).
- L1-8: 7. No match. #7204 and #8656 are nearby.
- L1-9: 4. #8787 (nested equality filters) may overlap, and #9074 touches the same line.
- L1-10: 8. No hits.
- L1-11: 8. No hits.
- L1-12: 7. Tests could collide with open CLI PRs (#9064, #9448).

| ID | score | rating |
|---|---|---|
| L1-1 | 8 | clear |
| L1-2 | 7 | clear (neighbors) |
| L1-3 | 9 | clear |
| L1-4 | 8 | clear |
| L1-5 | 8 | clear |
| L1-6 | 4 | likely leftover of #5442 |
| L1-7 | 8 | clear |
| L1-8 | 7 | clear |
| L1-9 | 4 | check #8787 / #9074 |
| L1-10 | 8 | clear |
| L1-11 | 8 | clear |
| L1-12 | 7 | clear |
