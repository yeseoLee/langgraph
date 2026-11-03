# LangGraph: project landscape (2026-11-03, main `3c916d7`)

- codegraph: 16,136 nodes / 51,888 edges.
- Packages: `libs/langgraph`, `checkpoint`, `checkpoint-postgres`, `checkpoint-sqlite`, `checkpoint-conformance`, `prebuilt`, `sdk-py`, `sdk-js`, `cli`.
- **Workflow (same as langchain):**
  - `require_issue_link.yml` / `reopen_on_assignment.yml`: an external PR needs `Fixes #N` and the author must be assigned to the issue.
  - `pr_lint.yml`: `type(scope):` title format.
  - The PR template is identical (1-2 sentences, English only, long AI-generated text gets closed).
- **Activity (09/15–11/02):**
  - Very active.
  - Most fixes come from maintainers (Elior Nataf Lackritz, Hunter Lovell, and others). They work in "series" on one area:
    - sdk-py SSE reconnect / retry (#9462, #9477, #9481, #9482, #9554)
    - DeltaChannel (#9257–#9302)
    - streaming namespaces (#9401, #9453, #9458, #9486)
  - External contributors: Sera (#9449), Vladislav Nechakhin (cli deps), Hugo DURAND (#9395), Saad Farooq (#9308).
- **Implications:**
  - Avoid core areas where maintainers are mid-series (fast fixes, high chance of collision).
  - Better targets: sync/async siblings of a merged fix, parity between savers (sqlite / postgres / memory), cli/sdk edge cases, docs/tests.
