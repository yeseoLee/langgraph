# Rebuttal 4: impact
- R4-1: Affects only users who pass tuples, which is off-type. The failure is loud and immediate at compile time, and the workaround is trivial (`list(...)`). Low impact on its own. Bundled with #9277 (a silent wrong pause), the combined fix matters more.
- R4-2: A bad key is caught at first write rather than at construction. Error locality improves, but there is no security loss, because pycryptodome rejects the key. The `-O` assert issue needs tampered or foreign-cipher rows, which are rare. Moderate value as hardening for an encryption feature people trust.
- R4-3: Users who want encrypted SQLite through the helper must build the connection themselves. That is a mild inconvenience, but common in tutorials. Moderate convenience value, zero correctness impact.

| ID | score | verdict |
|---|---|---|
| R4-1 | 3 | low alone |
| R4-2 | 5 | moderate hardening |
| R4-3 | 4 | convenience |
