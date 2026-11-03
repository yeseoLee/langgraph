# Round 2 CLI candidates (not executed)

## C1 fix(cli): env dict values are not escaped in generated docker-compose YAML
- Where: libs/cli/langgraph_cli/config.py:1809-1810 (`config_to_compose`): `f'{k}: "{v}"'`.
- Repro (not run): langgraph.json `"env": {"GREETING": "say \"hi\"", "WINPATH": "C:\\tmp\\x"}` -> `langgraph up`. Output `GREETING: "say "hi""` is invalid YAML; backslashes become YAML escapes (`\t`) and silently change value. Non-string values (ints/bools) become strings (OK) but `None` becomes "None".
- Fix: emit via `json.dumps(str(v))` (JSON string is valid YAML double-quoted scalar); also quote keys.
- Test: unit test on `config_to_compose` with quote/backslash values; parse output with yaml.safe_load and assert round-trip.

## C2 fix(cli): single quote in config JSON breaks Dockerfile `ENV X='...'`
- Where: config.py:1262-1295 (`_build_runtime_env_vars`): `ENV LANGSERVE_GRAPHS='{json.dumps(...)}'` (also STORE/AUTH/HTTP/UI/etc). json.dumps does not escape `'`.
- Repro (not run): graph spec dict with `"description": "User's agent"`, or http/auth config string containing `'` -> `langgraph build`/`dockerfile` -> Dockerfile ENV line terminated early; build fails or value is corrupted.
- Fix: replace `'` with `'"'"'`, or use `ENV KEY="..."` with json.dumps of the JSON string (escape `"`/`\`/`$`).
- Test: `config_to_docker` with an apostrophe in a graph description; assert the ENV value round-trips through shlex.split.

## C3 fix(cli): `langgraph dockerfile --add-docker-compose` hardcodes `env_file: [.env]`, ignoring config `env`
- Where: libs/cli/langgraph_cli/cli.py:611 sets `env_file = [".env"]` unconditionally; config `env` (dict or custom path like `./config/.env.prod`, documented at cli.py:54) is ignored. `langgraph up` honors it via config_to_compose (config.py:1809-1812). Also compose fails if `.env` doesn't exist.
- Fix: if `config["env"]` is str -> env_file [that path]; if dict -> add to `environment`; else omit env_file.
- Test: CliRunner `dockerfile --add-docker-compose` with dict env and custom env path; assert generated docker-compose.yml content.

## Dup checks (gh search, is:issue and is:pull-request each)
- "compose env quote", "env escaping docker-compose", "env_file dockerfile add-docker-compose", "LANGSERVE_GRAPHS quote", "single quote Dockerfile ENV", "apostrophe langgraph.json", "dockerfile json quote": no results (only #7447 unrelated "chore: gocli").
- "langgraph up env", "env dict docker", "add-docker-compose", "env_file": unrelated only (#7276 random postgres password, #9430 CRLF line endings, #8080 distributed postgres-uri, #2222, #5649, #8309 path traversal). No dups.
