# Round 1 core candidates (repros NOT executed)

## 1. StateGraph.add_edge accepts an empty start list silently
- libs/langgraph/langgraph/graph/state.py:969 (list branch of add_edge)
- Repro: `g.add_edge([], "b")` -> adds waiting edge ((), "b") with no error; at compile it creates a join channel with an empty barrier, so "b" never fires or behaves confusingly.
- Fix: `if not start_key: raise ValueError("add_edge requires at least one start node")` (mirrors add_sequence's "Sequence requires at least one node.").
- Test: pytest.raises(ValueError) for add_edge([], "b"); and add_edge(("a",)...) still works.
- Ref: input-validation pattern like add_sequence empty check; #9318 (router validation error).

## 2. add_conditional_edges does not reject END as source (parity with add_edge)
- libs/langgraph/langgraph/graph/state.py:1020 region (no source checks)
- Repro: `g.add_conditional_edges(END, router)` -> no error at call; compile fails later with "Found edge starting at unknown node '__end__'". add_edge raises "END cannot be a start node" immediately.
- Fix: early `if source == END: raise ValueError("END cannot be a start node")`.
- Test: pytest.raises(ValueError, match="END cannot be a start node").
- Ref: #9318 (merged, useful router errors), #6888.

## 3. EphemeralValue concurrent-update error lacks error_code / troubleshooting link
- libs/langgraph/langgraph/channels/ephemeral_value.py:64
- LastValue.update uses create_error_message(..., ErrorCode.INVALID_CONCURRENT_GRAPH_UPDATE); EphemeralValue(guard=True) raises a bare InvalidUpdateError string.
- Fix: wrap with create_error_message + same ErrorCode.
- Test: two updates to EphemeralValue -> error message contains INVALID_CONCURRENT_GRAPH_UPDATE.

## Duplicate checks (issue + pull-request each)
- "add_edge empty list", "add_edge empty start_key": no relevant hits (unrelated #8686,#9326,#6122).
- "add_conditional_edges END source", "conditional edge from END": hits #9327,#9319,#9173,#6540,#7691,#6340; PRs #9318,#6888,#7064,#6751,#6425 -- none about END as source.
- "EphemeralValue error_code", "EphemeralValue guard InvalidUpdateError": zero hits.
