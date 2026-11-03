# Round 2 core find (nothing executed)

GitHub search hit the rate limit partway through (HTTP 403 secondary + primary limit). Dup checks marked INCOMPLETE must be re-run before anyone works on them.

## SURVIVOR C2-1: fix(langgraph): `RemoteGraph._sanitize_config` drops `None` values and shifts list elements
- File: libs/langgraph/langgraph/pregel/remote.py:90-109 (`_sanitize_config_value`), used at :415-432.
- Bug: `None` means "not serializable, drop it", but a real JSON `null` value also comes back as `None`, so it gets dropped too.
  - `metadata={"a": None}` / `configurable={"x": None}` -> the key disappears (the server then sees a missing key, not an explicit null).
  - Lists: `[1, None, 3]` -> `[1, 3]`, which changes positional meaning; a non-serializable item in a list is also removed without warning, which shifts later indices.
- Repro (not run): `RemoteGraph("a", url="http://x")._sanitize_config({"configurable": {"k": None, "l": [1, None, 2]}})` -> `{"configurable": {"l": [1, 2]}}`.
- Fix: return a `_DROP` sentinel for unsupported types instead of `None`; pass `None` through as a primitive; filter on the sentinel (dicts/lists). Optional: keep lists aligned (decide whether an unsupported list item becomes `None` or is dropped).
- Test: libs/langgraph/tests/test_remote_graph.py, assert that None keys and list positions survive and that an `object()` value is still dropped.
- Dup queries: issue/PR "RemoteGraph+sanitize+None" -> only #7113 (a2a feature, unrelated); "RemoteGraph+config+None+dropped" -> #2559, #9252, #7113 (unrelated); "_sanitize_config_value" -> none. Variants "RemoteGraph+configurable+None" and "RemoteGraph+list+None" hit the RATE LIMIT, so these are INCOMPLETE.

## DROPPED (duplicates)
- RedisCache.clear(namespaces) prefix/wildcard semantics vs InMemory/Sqlite exact match (libs/checkpoint/langgraph/cache/redis/__init__.py clear): dups #8852 (`clear([()])` wipes all), #9104, #8851, PRs #9107/#9193/#9199.
- tools_condition / ToolNode._parse_input / ValidationNode._get_message raise IndexError on `[]` (prebuilt tool_node.py:1681, :1264; tool_validator.py:179): PRs #7876 and #7877 were closed, so the topic is covered or was rejected. ValidationNode is the same pattern (not searched separately).
- Command.__repr__ (types.py:860-865) uses asdict (deepcopy/pickle) and `if value`, which hides falsy `resume=0/False/""`: issue #9175 (open) covers the asdict part. The falsy-hiding part could be added to a fix for #9175 rather than filed alone. PR-side search: RATE LIMITED.

## Noted, not pursued (weak)
- RetryPolicy has no validation (max_attempts<=0, negative intervals), while TimeoutPolicy validates (types.py:427 vs :449). Behavior change, so it needs maintainer intent first.
- CachePolicy(ttl=0/negative): InMemory expires the entry at once; RedisCache `setex` errors and the error is swallowed (no caching). Inconsistent but minor.
