<!-- markdownlint-disable -->

# Hardening Report: PThorpe92--fossier/v0.0.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **PThorpe92--fossier/v0.0.3** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Three `uses:` references in action.yml use mutable version tags instead of pinned full-length SHA commit digests, making the action vulnerable to supply-chain attacks if the upstream tag is moved or compromised: `astral-sh/setup-uv@v4`, `actions/setup-python@v5`, and `actions/cache@v4`. Each should be pinned to a 40-character hex SHA (e.g. `actions/cache@1bd1e32a3bdc45362d1ceec05375cc4a7f3ba8b # v4`).

Locations:

- `action.yml:54`
- `action.yml:57`
- `action.yml:66`

### script-injection (severity: high)

Rule (a) violation: The 'Install fossier' step interpolates a `${{ }}` expression directly inside a `run:` shell command string: `run: uv pip install --system ${{ github.action_path }}`. Any `${{ ... }}` expression directly inside a `run:` block is subject to YAML template substitution before the shell ever sees it, making it a script-injection risk. The value should be passed via an `env:` variable and referenced as `$ACTION_PATH` (or equivalent) inside the script.

Locations:

- `action.yml:63`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Pinned all three unpinned `uses:` references to full 40-character SHA digests with tag comments: `astral-sh/setup-uv@v4` → `38f3f104447c67c051c4a08e39b64a148898af3a`, `actions/setup-python@v5` → `a26af69be951a213d495a4c3e4e4022e16d87065`, `actions/cache@v4` → `0057852bfaa89a56745cba8c7296529d2fc39830`. Fixed script injection in the 'Install fossier' step by moving `${{ github.action_path }}` into an `env:` block as `ACTION_PATH` and referencing it as `"$ACTION_PATH"` in the shell command.

