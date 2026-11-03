# Round 3 find (repros not executed)

Dropped as duplicates during the search: add_messages REMOVE_ALL + format (#9326, PR #8423); entrypoint postponed annotations (#9518, PR #9519); bare KeyError for a path_map miss (#9319, #6770, PRs #9318/#9413); `_infer_handled_types` postponed union / non-class annotations (#9338, #7016, PRs #7154/#7059); tools_condition empty list (PR #7876).

## R3-1 fix(cli): `langgraph new` reports success on a non-200 response and tracebacks on network errors
- libs/cli/langgraph_cli/templates.py:104-125 (`_download_repo_with_requests`). Only `HTTPError` is caught. A `URLError` (offline, DNS, proxy) or a timeout surfaces as a raw traceback. If `response.status != 200`, nothing is extracted, yet `create_new` (line ~186) still prints "New project created".
- Repro: patch `urllib.request.urlopen` to raise `URLError("offline")`, then run `create_new(tmp, "new-langgraph-project-python")`. You get a traceback, not a clean red error and exit 1.
- Fix: catch `error.URLError` (HTTPError is a subclass) and `OSError`, then exit 1. For a non-200 status, report an error and exit non-zero.
- Test: in tests/unit_tests/cli, monkeypatch `request.urlopen` and assert `SystemExit(1)` and the message, for both cases.
- Dup queries: "langgraph new URLError" (issue: #8076, which covers deploy upload, a different path; PR: #8086, also deploy); "cli new offline traceback" (nothing relevant).

## R3-2 fix(cli): `langgraph new <path>` crashes with `NotADirectoryError` when the path is an existing file
- templates.py:144 calls `os.listdir(path)` after `os.path.exists(path)`. If the path is a file, that raises `NotADirectoryError` (a traceback).
- Fix: `if os.path.isfile(path): secho error; sys.exit(1)` before the listdir check, or use `os.path.isdir`.
- Test: create tmp file `f`, call `create_new(str(f), "<valid id>")`, assert `SystemExit(1)` and that urlopen was never called.
- Dup queries: "langgraph new NotADirectoryError" (none, issues and PRs); "cli new path file exists" (only unrelated results).

## R3-3 fix(langgraph): deprecated `Interrupt(ns="...")` given as a `str` hashes the characters joined by "|"
- libs/langgraph/langgraph/types.py:625-630. `isinstance(ns, Sequence)` is true for `str`, so `"|".join("a:b")` becomes `"a|:|b"`. The resulting id differs from `Interrupt.from_ns(v, "a:b")`.
- Fix: treat a `str` ns as already joined (`ns if isinstance(ns, str) else "|".join(ns)`).
- Test: `Interrupt("v", ns="a:b").id == Interrupt.from_ns("v", "a:b").id`. Also check that the list form keeps its current id.
- Caveat: this is a deprecated kwarg, so impact is low. Check what kind of value `ns` held in v0.4/0.5 (a list of str); a maintainer may call this not worth fixing.
- Dup queries: "Interrupt ns deprecated id" (issue #4973, the roadmap; PR #5405, a v1 cleanup, neither a match); "Interrupt ns string join" (no match); "Interrupt from_ns" (#6626/#6624/#8886, unrelated).

## R3-4 fix(prebuilt): `ToolNode(handle_tool_errors="")` re-raises instead of returning an empty error message
- libs/prebuilt/langgraph/prebuilt/tool_node.py:1019 and 1179 (also 1079/1247): `if not self._handle_tool_errors` treats `""` as disabled, while `_handle_tool_error` (line 439) supports any str.
- Fix: use `self._handle_tool_errors is False`, or an explicit `is_disabled` check, across all four sites.
- Test: a tool that raises ValueError, `ToolNode([t], handle_tool_errors="")`. Expect a ToolMessage with status="error" and content "", not a raised error.
- Caveat: weak and debatable semantics (a maintainer may say an empty string should mean off). Low priority.
- Dup queries: "handle_tool_errors empty string" (issue #8648, a different bug: an empty message for model-level validation errors); "handle_tool_errors empty str" (PR #8729, unrelated).
