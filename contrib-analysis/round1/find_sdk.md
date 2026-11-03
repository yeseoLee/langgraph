# sdk-py contribution candidates (round 1)

All are docstring/API-contract fixes; nothing executed (repro by reading code only).

## 1. Async `threads.join_stream` docstring example uses sync `for` and omits `await`; `stream_mode` undocumented
- `libs/sdk-py/langgraph_sdk/_async/threads.py:785-829` — `async def join_stream(...) -> AsyncIterator[StreamPart]` (a coroutine returning an async iterator, unlike `runs.join_stream` which is a plain `def`). Example at ~:809 shows `for chunk in client.threads.join_stream(...)`; copy-pasting raises `TypeError: 'coroutine' object is not iterable`. Correct usage: `async for chunk in await client.threads.join_stream(...)`.
- `stream_mode` is a parameter in both `_async/threads.py` and `_sync/threads.py:762` but missing from both Args sections.
- Sibling precedent: async `runs.join_stream` example (`_async/runs.py:1140`) correctly uses `async for`; series #9554/#9482 touched stream paths only.
- Fix: correct example, document `stream_mode` in both clients.
- Test idea: docstring-only; optional unit test with `httpx.MockTransport` asserting `await client.threads.join_stream(...)` yields parts via `async for` and sends `stream_mode` query + `Last-Event-ID` header (locks the documented contract).

## 2. Sync `runs.join_stream` docstring says Returns `None` and example never iterates
- `libs/sdk-py/langgraph_sdk/_sync/runs.py:1098-1154` — returns `Iterator[StreamPart]` (generator), but docstring `Returns: None`; example calls `client.runs.join_stream(...)` without iterating, so it does nothing (lazy generator; no request sent). Async sibling (`_async/runs.py:1107`) documents "The stream of parts" and iterates.
- Fix: `Returns: An iterator of stream parts.`; example `for part in client.runs.join_stream(...): print(part)`.
- Test idea: unit test with mock transport asserting no HTTP request occurs until iteration, and parts are yielded when iterated.

## 3. `runs.join` docstring says Returns `None` in both clients; it returns the final thread state `dict`
- `libs/sdk-py/langgraph_sdk/_async/runs.py:1070-1104` and `_sync/runs.py:1061-1097`: signature `-> dict`, summary "Returns the final state of the thread", but `Returns: None`. Async example also has typo `result =await`.
- Related merged work: #9481 changed `join` reconnect behavior but kept docstring.
- Fix: `Returns: The final state values of the thread.` in both; fix spacing typo.
- Test idea: mock transport returning JSON body for `/join`, assert `client.runs.join(...)` returns that dict (sync + async).

## Dropped
- Sync `threads.stream` docstring missing `run_start_timeout`: covered by open #9208 / closed PR #9210.

## Duplicate searches (search/issues, repo:langchain-ai/langgraph)
- `is:issue join_stream docstring` -> none; `is:pull-request` -> none
- `threads.join_stream` issue/PR -> none
- `join_stream async for` issue -> 9172 open (list stream modes; different), 8087 closed (v2 streaming); PR -> 8416, 8088 (v2 streaming; different)
- `join_stream returns None` issue -> 9172 (different); PR -> none
- `join_stream example iterate` issue -> 8087 (different); PR -> none
- `runs.join returns` / `runs.join docstring` issue -> 5442 closed "`runs.join` return type is improperly typed as `void`..." (JS SDK typing, `ThreadState["values"]` wording; not the Python docstring — verify before filing); PR -> 9399 (wait retry; different)
- `join returns None sdk` -> unrelated
- `sdk docstring example` PR -> 7408 closed "fix: missing f-string prefix and sync SDK docstring examples" (could not fetch file list, REST 403; current master still has the wrong text, so not fixed either way — mention when filing)
- `run_start_timeout` -> 9208 open, 9209 open, PR 9210 closed => dropped candidate
