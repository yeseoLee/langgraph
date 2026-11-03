# Rebuttal 5: process fit
Facts:
- ISSUE_TEMPLATE has only `bug-report.yml` and `privileged.yml` (plus config.yml). There is no feature-request template, so features go through discussion or a maintainer.
- `require_issue_link.yml` requires external PRs to link an approved issue and the author to be assigned to it. Every candidate therefore needs an issue first, and features face the hardest route to assignment.
- Valid pr_lint scopes: checkpoint, checkpoint-postgres, checkpoint-sqlite, cli, langgraph, prebuilt, scheduler-kafka, sdk-py, docs, ci, deps, deps-dev. Multi-scope titles like `checkpoint-sqlite,checkpoint` need checking against the action's parsing (comma-separated scopes are usually accepted). Single-scope titles are safer.
- No repro can be run here (no Python), so every repro needs to be written as a snippet in the bug template.

## Scores (10 = easiest path)
- L1-1: 7. Bug template fits, scope `langgraph`, trivial repro. Small fixes get assigned easily.
- L1-2: 6. Bug template is borderline because it is a UX improvement. Scope `langgraph`. Easy repro.
- L1-3: 4. Too small to justify an issue. Better folded into L1-1 or L1-2 as "validation parity" or dropped.
- L1-4: 7. Merge with L1-5 and L1-6 into one `docs(sdk-py)` PR and one issue. Bug template ("docs wrong") works.
- L1-5: 7. Merge into the L1-4 PR.
- L1-6: 6. Merge into the L1-4 PR and cite #5442.
- L1-7: 3. Feature with no template, so it needs maintainer approval. Large scope. Low chance of assignment.
- L1-8: 8. A clear bug template case with a 5-line repro. The title needs two scopes (`checkpoint-sqlite,checkpoint-postgres`), or split it.
- L1-9: 5. Bug, but it overlaps #9074 and #8787. Best handled by commenting on #9074 and proposing a combined fix.
- L1-10: 5. Feature, but small parity with #9395 and #9243. Needs an issue and approval. Scope `cli`.
- L1-11: 3. Feature that needs design input.
- L1-12: 4. Test-only PRs still need a linked issue, `test` is an allowed type. Weak story for assignment.

Recommended merges: L1-4+5+6 into one docs PR. L1-1+2(+3) into a single "graph builder validation parity" issue, or keep them separate if the maintainer prefers. L1-9 goes with #9074.

| ID | score | rating |
|---|---|---|
| L1-1 | 7 | good |
| L1-2 | 6 | ok |
| L1-3 | 4 | fold in |
| L1-4 | 7 | merge 4/5/6 |
| L1-5 | 7 | merge |
| L1-6 | 6 | merge, cite #5442 |
| L1-7 | 3 | needs approval |
| L1-8 | 8 | best |
| L1-9 | 5 | coordinate #9074 |
| L1-10 | 5 | needs approval |
| L1-11 | 3 | needs design |
| L1-12 | 4 | weak |
