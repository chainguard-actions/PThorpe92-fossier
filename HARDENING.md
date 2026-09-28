<!-- markdownlint-disable -->

# Hardening Report: PThorpe92--fossier/v0.0.5

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **PThorpe92--fossier/v0.0.5** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Three `uses:` references in action.yml use mutable tag refs instead of full 40-character SHA commit hashes, making the action vulnerable to supply-chain attacks if those tags are moved. Failing references: `astral-sh/setup-uv@v4`, `actions/setup-python@v5`, `actions/cache@v4`. Each should be pinned to a full commit SHA (e.g. `astral-sh/setup-uv@d4b5f9a...`).

Locations:

- `action.yml:50`
- `action.yml:53`
- `action.yml:62`

### script-injection (severity: high)

Sub-rule (a): A `${{ ... }}` expression is directly interpolated inside a `run:` shell command string in the 'Install fossier' step. The offending line is: `run: uv pip install --system ${{ github.action_path }}`. Any `${{ ... }}` expression embedded directly in a run block undergoes YAML template substitution before the shell sees it, bypassing shell quoting. The fix is to pass the value via an `env:` variable and reference it as a quoted shell variable: `env: ACTION_PATH: ${{ github.action_path }}` then `run: uv pip install --system "$ACTION_PATH"`.

Locations:

- `action.yml:59`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Fixed all findings in hardened/action/action.yml:
1. Pinned `astral-sh/setup-uv@v4` → `@38f3f104447c67c051c4a08e39b64a148898af3a # v4`
2. Pinned `actions/setup-python@v5` → `@a26af69be951a213d495a4c3e4e4022e16d87065 # v5`
3. Pinned `actions/cache@v4` → `@0057852bfaa89a56745cba8c7296529d2fc39830 # v4`
4. Fixed script injection in 'Install fossier' step: moved `${{ github.action_path }}` to an `env:` block as `ACTION_PATH` and referenced it as `"$ACTION_PATH"` in the shell command.

