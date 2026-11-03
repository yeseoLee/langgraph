# Rebuttal 4: user impact and fix risk
Scale: 10 = high value and low risk.

- L1-1: 5. Edge case. Raising early could break code that "works" by accident, though an empty start list is almost certainly a bug anyway.
- L1-2: 6. Moves an existing error from compile time to `add_conditional_edges`. Graphs that fail today still fail, just earlier, so the only cost is a message change.
- L1-3: 4. Cosmetic. Adds an error code or link. Very low risk and low value.
- L1-4: 7. The async example is wrong as written, so users copying it get a coroutine or generator bug. Docs only, zero risk.
- L1-5: 7. A wrong return description misleads IDE users. Zero risk.
- L1-6: 5. Zero risk, but value is low if #5442 already fixed the types.
- L1-7: 6. Useful for sqlite and dev users who want cleanup. Adds new API surface, and the `keep_latest` and DeltaChannel semantics are risky. Large surface, plus conformance changes.
- L1-8: 7. A silent wrong result (0 rows) is a real bug. The fix changes results for anyone relying on 0, which is unlikely to be intended. It touches two packages.
- L1-9: 6. Silent misses on dict filters. A Python-side post-filter changes performance and limit semantics (limit applied before filtering), so the SQL approach needs care.
- L1-10: 6. Additive flag. Helps scripting. No breaking risk.
- L1-11: 5. Additive. Streaming log format choices need design.
- L1-12: 5. No user-facing change. Raises coverage only, so the risk is nil and the value is modest.

| ID | score | rating |
|---|---|---|
| L1-1 | 5 | low-med |
| L1-2 | 6 | medium |
| L1-3 | 4 | low |
| L1-4 | 7 | good, safe |
| L1-5 | 7 | good, safe |
| L1-6 | 5 | safe, low value |
| L1-7 | 6 | valuable, risky |
| L1-8 | 7 | real bug, small fix |
| L1-9 | 6 | real bug, tricky fix |
| L1-10 | 6 | safe additive |
| L1-11 | 5 | design needed |
| L1-12 | 5 | safe, low visibility |
