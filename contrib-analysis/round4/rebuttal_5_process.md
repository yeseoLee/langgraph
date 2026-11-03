# Rebuttal 5: process fit
Facts (round1 rules): an external PR needs an approved, assigned issue (`require_issue_link.yml`). There is only a bug-report template. Scopes include langgraph, checkpoint, checkpoint-sqlite. No repro can be executed here, so snippets must be written in the template.
- R4-1: Bug template fits, scope `langgraph`. But #9277 is open on the same code, so the right process is to comment on #9277 offering a combined fix and ask for assignment. A separate issue risks closure as a dup. The hint widening to `Sequence[str]` is a signature change, so warn about it in the PR.
- R4-2: Clean bug-template case (3-line repro, `ValueError` expected at construction). Scope `checkpoint`. Mention #9470 to avoid conflicting edits to `from_pycryptodome_aes`.
- R4-3: This is a feature, and there is no feature template. It needs maintainer buy-in, and the Postgres precedent (3 closed PRs) suggests asking in a discussion first. Scope `checkpoint-sqlite`. Additive keyword-only signature change: warn.

| ID | score | verdict |
|---|---|---|
| R4-1 | 5 | via #9277 comment |
| R4-2 | 7 | file bug, scope checkpoint |
| R4-3 | 3 | needs approval first |
