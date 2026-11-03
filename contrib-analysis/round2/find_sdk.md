# Round 2: SDK (libs/sdk-py/langgraph_sdk) bug candidates

Method: normalized diff of `_async/*.py` vs `_sync/*.py` (async/await stripped). Payload building, `_encode_json`/`_decode_json` (empty body -> None in both), `_raise_for_status_typed`, timeout and `get_client`/`get_sync_client` handling all match between the two clients. So the bugs below are in shared logic that both clients have, not sync/async splits. Nothing was executed.

## S2-1 fix(sdk-py): `threads.search(ids=[])` returns every thread instead of none
- Async: `_async/threads.py` `search` (~L297), `if ids: payload["ids"] = ids`. Sync: `_sync/threads.py:353`, same code.
- Bug: an empty list fails the truthiness check, so the filter is left out of the payload and the server returns unfiltered results (up to `limit`). Callers that build `ids` from an empty lookup get unrelated threads back, which can leak data across tenants or users in app code.
- Repro (not executed): `client.threads.search(ids=[])` sends `{"limit":10,"offset":0}`.
- Fix: `if ids is not None:`. Optionally return `[]` on the client side without a request.
- Test: mock transport, assert that the request JSON contains `"ids": []` (both clients).
- Dup queries: `threads+search+ids+empty` (issue: unrelated 8754/8836/...; PR: none); `search+ids+empty+list` (issue: unrelated; PR: 9149 deps bump). No match.

## S2-2 fix(sdk-py): `crons.count` has no `enabled` filter, so it can't match `crons.search`
- Async: `_async/cron.py` `count` (~L519-544): only assistant_id/thread_id/metadata. `search` (~L490) sends `enabled`. Sync: `_sync/cron.py:498-528`, same.
- Bug: there is no way to count only enabled or only disabled crons. The pagination totals from `count` don't agree with a filtered `search`.
- Fix: add keyword-only `enabled: bool | None = None` to `count` in both clients and send it when not None. Check that the server's `/runs/crons/count` accepts `enabled` first (needs verification; this is a gap, not a confirmed contract).
- Test: mock transport, assert that `enabled` is in the count payload.
- Dup queries: `crons+count+enabled` (issue: none; PR: 7840 unrelated); `cron+count` (issue: unrelated; PR: 7737 closed "feat(sdk-py): support metadata filter for crons search/count", which added metadata only, not enabled). Reference #7737 as prior art. No direct dup.

## S2-3 fix(sdk-py): `assistants.update(name="" / description="")` is silently dropped
- Async: `_async/assistants.py:441-444` (`if name:` / `if description:`); same truthiness in create (L377-380). Sync: `_sync/assistants.py:443-446` (and 381-384).
- Bug: you can't clear an assistant's description by setting it to "". The PATCH body omits the field and returns success with no change. `config`/`context` already use `is not None` in the same function, so the behavior is inconsistent.
- Fix: `if name is not None` / `if description is not None` in update. Server behavior for an empty string needs checking.
- Test: mock transport, assert that the PATCH JSON has `"description": ""`.
- Dup queries: `update+assistant+description` (issue/PR: unrelated); `assistants+update+description+empty` (issue: 740 unrelated; PR: none); `assistants.update+name` (none). No match.

## Dropped
- Reserved `x-api-key` header check is case-sensitive (`_shared/utilities.py:22,58`). Dup of #8378 (issue, closed) / #8379, #8385 (PRs, closed). The code still looks case-sensitive, so it's worth checking whether those PRs were merged, but it's dropped as a dup.
- cron `multitask_strategy` handling differs (async `if`, sync None-filter). It behaves the same, so it's not a bug.
