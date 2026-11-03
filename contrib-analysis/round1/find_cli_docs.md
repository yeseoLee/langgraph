# Round 1: libs/cli candidates

## 1. feat(cli): add `--json` to `deploy revisions list`
- Where: libs/cli/langgraph_cli/deploy.py:2694-2724 (`deploy_revisions_list`). It only prints `REVISIONS_TABLE`, or the text "No revisions found ...".
- Evidence: `deploy list` (#9395, around deploy.py:2588) and `deploy listeners list` (#9243, around deploy.py:2648) both have a `--json` flag, and both pass `no_input=json_output` to `_create_host_backend_client` and `_call_host_backend_with_optional_tenant`. `revisions list` is the only `list` subcommand left without the flag.
- Fix: add `--json`/`json_output` the same way. Print `json.dumps([...])` of the revision rows (id, status after `_with_replaced_status`, created_at), print `[]` instead of the text message when there are no revisions, and turn on no_input.
- Test: in tests/unit_tests/cli/test_deploy_command.py, use CliRunner with a mocked `HostBackendClient.list_revisions` to check that the output is valid JSON, that an empty result gives `[]`, and that no prompt appears.
- Dup queries (issue + PR): "revisions list json", "deploy revisions json", "revisions --json". The only hit was #4724, which is closed and unrelated. No duplicate.

## 2. feat(cli): add `--json` (JSON Lines) output to `deploy logs`
- Where: deploy.py:2829-2930 (`deploy_logs`). `_print_entries` sends each entry through `format_log_entry` + `secho` with colors. Raw entries cannot be read by machines, and with `--follow` the output cannot be piped to `jq`.
- Evidence: this follows the same parity direction as #9395 and #9243. #9308 (preserve failed build logs) touched the same command.
- Fix: add a `--json` flag that prints `json.dumps(entry)` for each entry, one per line, with no color. It should work together with `--follow`. Set no_input.
- Test: CliRunner with mocked `get_deploy_logs`/`get_build_logs`. Check that each line parses with `json.loads`, and that the `--type build` path is covered.
- Dup queries: "deploy logs json" (issue: only unrelated runtime issues; PR: #9395, #9308, #7447, #6589, none of which add JSON to logs), "logs jsonl cli" (no results). No duplicate.

## 3. test(cli): CliRunner coverage for the `langgraph validate` command
- Where: libs/cli/langgraph_cli/cli.py:872-905. The command has several branches: invalid JSON leads to a UsageError; a validation error prints red text to stderr, prints the unknown-key warnings to stderr, and exits with code 1; a valid config gives the message with graph-count pluralization ("1 graph" vs "N graphs"); a valid config can also print unknown-key warnings to stdout.
- Evidence: a grep of tests/ for `"validate"` found no CliRunner invocation of the command. The only test of `get_unknown_keys` is the empty-result assertion at tests/unit_tests/test_config.py:1309. The typo suggestion branch ("did you mean") and the "not a recognized config field" branch are not tested.
- Fix: tests only. Add tests/unit_tests/cli/test_validate.py with tmp_path configs that cover each branch, plus parametrized `get_unknown_keys` cases (for example `"graph"` should suggest `"graphs"`, and `"zzz"` should give the unrecognized-field message).
- Dup queries: "langgraph validate command test" and "cli validate unknown keys". Neither returned anything relevant: only unrelated runtime issues and dependency-bump PRs, plus #9502 (login) and #5284 (dev shortcut). No duplicate.

Not covered: the schema vs docker-generation key mismatch audit and the README link audit.
