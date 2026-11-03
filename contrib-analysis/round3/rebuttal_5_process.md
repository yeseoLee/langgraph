# Rebuttal 5: process fit
Facts (from round 1): only the `bug-report.yml` template applies. `require_issue_link.yml` requires an approved, assigned issue for external PRs. Valid scopes include `cli`, `langgraph`, `prebuilt`. No Python here, so repros must be written as snippets.
- L3-1: Bug template fits. Scope `cli`. The repro (monkeypatch `urlopen` to raise `URLError`) is easy to write, and tests go in libs/cli/tests/unit_tests. The issue must cite #8075/#8111, or it risks closure as a duplicate.
- L3-2: Bug template fits, but it is too small to stand alone. **Merge L3-1 and L3-2 into one issue and one PR**: "fix(cli): `langgraph new` exits cleanly on network errors and invalid target paths". It is the same file, the same function family, and the same reviewer, with one test module. Yes, merge.
- L3-3: The bug template is awkward because a deprecated API with off-contract input invites "won't fix". Scope `langgraph`. If raised at all, raise it as a question on the issue first.
- L3-4: Bug template fits. Scope `prebuilt`. The issue must first settle the semantics question (is `""` off or empty?), and assignment is unlikely until a maintainer picks one. You could ask about it in #8648 instead.

| ID | score | verdict |
|---|---|---|
| L3-1 | 7 | good; merge with L3-2, cite #8075 |
| L3-2 | 6 | merge into L3-1 PR |
| L3-3 | 2 | ask first / skip |
| L3-4 | 4 | needs semantics decision |
