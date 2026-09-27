<!-- markdownlint-disable -->

# Hardening Report: PThorpe92--fossier/v0.0.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **PThorpe92--fossier/v0.0.3** was hardened automatically. 2 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Three `uses:` references in action.yml use mutable version tags instead of pinned 40-character SHA commit hashes, making the action vulnerable to supply-chain attacks if the referenced tags are moved or overwritten: `astral-sh/setup-uv@v4` (line 54), `actions/setup-python@v5` (line 57), `actions/cache@v4` (line 66). Each should be pinned to a full SHA, e.g. `actions/cache@<40-hex-sha> # v4`.

Locations:

- `action.yml:54`
- `action.yml:57`
- `action.yml:66`

### script-injection (severity: high)

Sub-rule (a): The 'Install fossier' step directly interpolates a GitHub Actions expression `${{ github.action_path }}` inside a `run:` shell command string: `run: uv pip install --system ${{ github.action_path }}`. Any `${{ ... }}` expression interpolated directly into a `run:` block passes through YAML template substitution before the shell sees it, bypassing shell quoting and enabling script injection. The value should be passed via an `env:` variable and referenced as a quoted shell variable instead.

Locations:

- `action.yml:63`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Pinned all three unpinned `uses:` references to full 40-character SHA hashes: `astral-sh/setup-uv@v4` → `38f3f104447c67c051c4a08e39b64a148898af3a`, `actions/setup-python@v5` → `a26af69be951a213d495a4c3e4e4022e16d87065`, `actions/cache@v4` → `0057852bfaa89a56745cba8c7296529d2fc39830`. Fixed script injection in the 'Install fossier' step by moving `${{ github.action_path }}` into an `env:` variable `ACTION_PATH` and referencing it as `"$ACTION_PATH"` in the shell command.

### Iteration 2

**Fixes applied:** github-env-injection, script-injection

**Notes:**

Fixed two related security findings:

1. github-env-injection (comment_commands.py): Added `_sanitize_env_value()` helper that strips `\r` and `\n` characters. All four FOSSIER_TRUST_* values (branch, commit_msg, pr_title, pr_body) are now sanitized before being written to $GITHUB_ENV, preventing newline injection from attacker-controlled GitHub usernames.

2. script-injection (action.yml): In the 'Open trust update PR' step, added shell-level sanitization using `printf '%s' "$VAR" | tr -d '\n\r'` for all four FOSSIER_TRUST_* variables before using them in git/gh commands. The sanitized SAFE_* variables are used throughout the step instead of the raw inherited env vars. This provides defense-in-depth alongside the Python-level fix.

