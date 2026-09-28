<!-- markdownlint-disable -->

# Hardening Report: PThorpe92--fossier/v0.0.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **PThorpe92--fossier/v0.0.3** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): A ${{ }} expression is interpolated directly inside a run: shell command. The step 'Install fossier' uses `run: uv pip install --system ${{ github.action_path }}`, which injects the github.action_path context value directly into the shell command string before the shell ever sees it. Any expression inside ${{ }} in a run: block is a script-injection risk regardless of which context it reads from.

Locations:

- `action.yml:55`

### unpinned-uses (severity: high)

Three uses: references in action.yml use mutable version tags instead of immutable 40-character SHA digests, making the action vulnerable to supply-chain attacks if those tags are moved: `astral-sh/setup-uv@v4` (line 47), `actions/setup-python@v5` (line 50), `actions/cache@v4` (line 58).

Locations:

- `action.yml:47`
- `action.yml:50`
- `action.yml:58`

### github-env-injection (severity: high)

The Python helper `_signal_trust_change` in comment_commands.py writes values derived from GitHub event data directly to $GITHUB_ENV without newline sanitization. The `branch`, `commit_msg`, `pr_title`, and `pr_body` arguments all incorporate attacker-controlled data: `pr_author` (the PR author's GitHub username), `self.commenter` (the comment author's username), `self.pr_number`, and `reason` (free-text from the comment body). These are written with bare f-strings such as `f.write(f"FOSSIER_TRUST_BRANCH={branch}\n")` with no `printf '%s' ... | tr -d '\n\r'` sanitization, allowing a malicious actor to inject additional environment variable definitions by embedding newlines in a username or comment body.

Locations:

- `src/fossier/comment_commands.py:65`
- `src/fossier/comment_commands.py:66`
- `src/fossier/comment_commands.py:67`
- `src/fossier/comment_commands.py:68`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, github-env-injection

**Notes:**

1. Pinned astral-sh/setup-uv@v4 → @38f3f104447c67c051c4a08e39b64a148898af3a # v4; actions/setup-python@v5 → @a26af69be951a213d495a4c3e4e4022e16d87065 # v5; actions/cache@v4 → @0057852bfaa89a56745cba8c7296529d2fc39830 # v4 in action.yml. 2. Moved ${{ github.action_path }} out of the run: shell string into an env: block as ACTION_PATH, referenced as "$ACTION_PATH" in the shell command. 3. Added _sanitize_env_value() helper in comment_commands.py that strips \r and \n characters, and applied it to all four values (branch, commit_msg, pr_title, pr_body) before writing to $GITHUB_ENV to prevent newline injection attacks.

