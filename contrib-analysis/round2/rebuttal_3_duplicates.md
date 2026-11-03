# Rebuttal 3: duplicates (re-searched with new keywords, 2026-11-03)
Method: gh search/issues, is:issue and is:pull-request each, `sleep 3` between queries, no rate-limit hits this time. Direct REST (`repos/...`) is blocked in this session, so #8902 was read through WebFetch.

## Findings
- L2-1: "docker-compose yaml quote environment", "cli env backslash" -> nothing. #6983 (open PR, POSIX paths for auth) is unrelated. Clean.
- L2-2: "Dockerfile ENV escape" -> #6341 and #5435, both unrelated. Clean.
- L2-3: "dockerfile compose env" -> #7276, #9063 (closed feat: agent flags/env defaults, should be checked to see if it touched add-docker-compose), and #9448 (open PR: build cache on env hash, adjacent). Mostly clean.
- L2-4: "threads.search ids" -> none. Clean.
- L2-5: "crons enabled count" -> none (#7840 is unrelated). Prior art is still #7737. Clean.
- L2-6: "assistant description empty string" -> noise only. Clean.
- L2-7 (priority): new queries "RemoteGraph sanitize", "RemoteGraph metadata null", "RemoteGraph list config", "remote graph configurable serializ", "RemoteGraph None". Hits: #5516 (closed PR: preserve run_id/run_name in sanitization, a different field), #5510, #8870 (limit=0, other bug), #9011, #9252, #7113. Nothing on None or list shifting. The dup check is now COMPLETE, and the candidate is clean. Cite #5516 as the history of `_sanitize_config`.
- L2-8: "sqlite3 datetime adapter" -> none. "expires_at sqlite" -> #8806 and #9558 (open: TTL refresh in batches), #9559 (closed PR), #9497 (postgres fractional TTL). These are adjacent TTL work in the same code region, with no dup of the adapter/format bug. A fix may conflict with #9559/#8806 changes.
- L2-9 (priority): "ghost namespace", "list_namespaces delete", "InMemoryStore empty namespace" -> #8734 (open), plus TWO closed PRs for it: #8735 and #8767 ("stop InMemoryStore reads from creating namespaces"). Neither covers deletes. Still a NEAR-DUP: it is the same symptom and the same `list_namespaces` contract. Comment on #8734 and ask to widen scope, rather than opening a new issue.
- L2-10 (priority): #8902 was closed as **not planned** by maintainer eliornl on 2026-10-31 with "feature requests go on the langchain forum", even though it was filed as a bug. It was never assigned. #8903 was auto-closed by require-issue-link (no assignment), and #8904/#8905 were closed the same way. #8354 added omit_expired for postgres only. A maintainer explicitly declined it, so re-raising is effectively a DUP, and a reopen would go against a maintainer decision. Only the forum route remains.

| ID | score | rating |
|---|---|---|
| L2-1 | 9 | clean |
| L2-2 | 9 | clean |
| L2-3 | 8 | clean (check #9063) |
| L2-4 | 9 | clean |
| L2-5 | 8 | clean, cite #7737 |
| L2-6 | 9 | clean |
| L2-7 | 8 | clean now, cite #5516 |
| L2-8 | 7 | clean, adjacent #8806/#9559 |
| L2-9 | 3 | near-dup #8734 (+#8735/#8767) |
| L2-10 | 1 | dup, declined (not planned) |
