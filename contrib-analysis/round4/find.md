# Round 4 find (repros not executed)

## R4-1 fix(langgraph): `StateGraph.compile()` raises `TypeError` when `interrupt_before`/`interrupt_after` is a tuple
- Where: libs/langgraph/langgraph/graph/state.py:1257-1265
  `(interrupt_before if interrupt_before != "*" else []) + interrupt_after` concatenates the raw argument with a list. Downstream (`Pregel`, `_validate.validate_graph`) accepts any `Sequence[str]`.
- Repro: `g = StateGraph(S); g.add_node("a", f); g.add_edge(START, "a"); g.compile(interrupt_before=("a",))` -> `TypeError: can only concatenate tuple (not "list") to tuple`. Same with `interrupt_after=("a",)` (`[] + ("a",)`).
- Fix: build the list with `[*interrupt_before, *interrupt_after]` (keeping the `"*"` handling), or normalize with `list(...)` when not `"*"`. Optionally widen the hint to `Sequence[str]` (signature change: warn).
- Test: tests/test_state.py style: compile with tuple args for both params, assert it compiles and that `graph.interrupt_before_nodes == ("a",)` or the run pauses before "a" with InMemorySaver.
- Note: also when `interrupt_after == "*"`, `interrupt_before` names are not passed to `self.validate(interrupt=...)` (still caught later by `validate_graph`), so worth one line in the PR.
- Dup: `is:issue interrupt_before tuple` -> nothing relevant (8629, 7127, 6486...); `is:pull-request interrupt_before tuple` -> 8637, 8548 (unrelated); `interrupt_after tuple` -> unrelated; `compile interrupt_before concatenate` -> 9277 open (string `"node_one"` treated as chars: related area, different bug; a fix could cover both, mention it), 2928; `"can only concatenate"` -> 978 closed (old plan-and-execute example, unrelated).

## R4-2 fix(checkpoint): `EncryptedSerializer.from_pycryptodome_aes(key=...)` skips the key-length check and uses `assert` for the cipher name
- Where: libs/checkpoint/langgraph/checkpoint/serde/encrypted.py:51-59 (length check only on the `LANGGRAPH_AES_KEY` path) and :72 (`assert ciphername == "aes"`).
- Repro: `EncryptedSerializer.from_pycryptodome_aes(key=b"short")` succeeds; the failure appears only at the first `dumps_typed` (pycryptodome `ValueError: Incorrect AES key length`) deep inside a checkpoint write. Under `python -O`, the assert is removed, so a row tagged with another cipher name is decrypted as AES (error message lost).
- Fix: validate `len(key) in (16, 24, 32)` for both paths (also reject `str` key with a clear message); replace the assert with `if ciphername != "aes": raise ValueError(...)`.
- Test: tests/test_encrypted.py: `pytest.raises(ValueError)` for `key=b"short"` at construction; `loads_typed(("json+other", b"..."))` raises ValueError.
- Dup: `EncryptedSerializer key` (issue) -> 9470 open (mac_len), 8938, 9004, 9099, 8953: none about key length/assert; `pycryptodome key validation` -> 9470 only; `EncryptedSerializer assert`, `Unsupported cipher`, `AES key bytes long` -> nothing new. Risk: 9470/9495 touch the same function; coordinate (9495 closed).

## R4-3 fix(checkpoint-sqlite): `SqliteSaver`/`AsyncSqliteSaver.from_conn_string` have no `serde` argument
- Where: libs/checkpoint-sqlite/langgraph/checkpoint/sqlite/__init__.py:104 and aio.py:138; both `yield cls(conn)`. Constructors accept `serde`; `AsyncPostgresSaver.from_conn_string` has `serde=`.
- Repro: no way to use `EncryptedSerializer` with the helper: `SqliteSaver.from_conn_string(":memory:", serde=s)` -> TypeError.
- Fix: add keyword-only `serde: SerializerProtocol | None = None` and pass it through (signature change, additive: warn).
- Test: `with SqliteSaver.from_conn_string(":memory:", serde=s) as c: assert c.serde is s`-ish (base wraps serde with maybe_add_typed_methods; assert put/get round-trip goes through s via a spy serde); async twin.
- Dup: `SqliteSaver from_conn_string serde`, `checkpoint-sqlite from_conn_string serde`, `AsyncSqliteSaver serde from_conn_string` (issue+PR) -> only 8116 (Postgres) and 1248 (unrelated). RISK: the Postgres equivalent was proposed 3 times (PRs 8117, 8233, 9162 all closed unmerged; issues 8116, 9118 closed) and sync PostgresSaver still lacks it, so maintainers may be declining this. Find out why before filing.

## Dropped (duplicates)
- `AsyncSqliteSaver.adelete_thread` missing `await self.setup()` (aio.py:618): issue 8549 open, PRs 8555/8571/8577/8643 closed.
- Shallow Postgres savers lack `delete_thread`: issue 8906 / PR 8909 (also deprecated).
- Sync `PostgresSaver.from_conn_string` lacks `serde`: 8116, 9118, PRs 8117/8233/9162.
- `list(limit=-1)` saver disagreement: issue 8656 open, PR 8678.
- sdk-py reserved `x-api-key` check is case-sensitive (_shared/utilities.py:57): issue 8378, PRs 8379/8385 (closed; code still case-sensitive).
- AES with OCB/CCM modes (nonce != 16 bytes, encrypted.py:73) cannot be decrypted: same root cause as issue 9470 (mac_len hardcoded slicing); fold into that one, not new.
