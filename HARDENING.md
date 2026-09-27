<!-- markdownlint-disable -->

# Hardening Report: PThorpe92--fossier/v0.0.5

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **PThorpe92--fossier/v0.0.5** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): A ${{ }} expression is directly interpolated inside a `run:` shell command string. On line 57, `${{ github.action_path }}` is embedded directly in the shell command `uv pip install --system ${{ github.action_path }}`. Although `github.action_path` is not attacker-controlled, any `${{ ... }}` expression inside a `run:` block undergoes YAML template substitution before the shell ever sees it, making it a script-injection risk. The value should be passed via an `env:` variable and referenced as `"$ACTION_PATH"` instead.

Locations:

- `action.yml:57`

### unpinned-uses (severity: high)

Three `uses:` references in action.yml use mutable tag refs instead of pinned full-length SHA commit hashes, making the action vulnerable to supply-chain attacks if those tags are moved or compromised: `astral-sh/setup-uv@v4` (line 51), `actions/setup-python@v5` (line 54), `actions/cache@v4` (line 63). Each should be pinned to a full 40-character hex commit SHA (e.g. `uses: actions/cache@1bd1e32a3bdc45362d1e726936510720a7c6158d # v4`).

Locations:

- `action.yml:51`
- `action.yml:54`
- `action.yml:63`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Fixed all three findings: (1) Pinned astral-sh/setup-uv@v4 to SHA 38f3f104447c67c051c4a08e39b64a148898af3a, (2) Pinned actions/setup-python@v5 to SHA a26af69be951a213d495a4c3e4e4022e16d87065, (3) Pinned actions/cache@v4 to SHA 0057852bfaa89a56745cba8c7296529d2fc39830. All original tags preserved as inline comments. (4) Fixed script-injection on line 57 by moving ${{ github.action_path }} into an env: block as ACTION_PATH and referencing it as "$ACTION_PATH" in the shell command.

