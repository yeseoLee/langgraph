# LangGraph Round 3 evaluation (M / T / D / I / P -> average; pass >= 5.5)

| ID | M | T | D | I | P | Avg | Result |
|---|---|---|---|---|---|---|---|
| L3-1 `langgraph new` URLError/BadZipFile traceback | 5 | 6 | 4 | 6 | 7 | 5.6 | **Pass (borderline)** -> merged with L3-2. Cite #8075 / #8111 (timeout, closed) |
| L3-2 `langgraph new <file>` NotADirectoryError | 3 | 6 | 9 | 4 | 6 | 5.6 | **Pass** (merged) |
| L3-3 Interrupt(ns=str) | 2 | 3 | 9 | 2 | 2 | 3.6 | Reject (outside the deprecated parameter's contract) |
| L3-4 handle_tool_errors="" | 3 | 5 | 7 | 3 | 4 | 4.4 | Reject (semantics are the maintainer's call) |

Correction: the "non-200 success" claim does not hold (urlopen raises HTTPError). Lead the issue with URLError / BadZipFile.
