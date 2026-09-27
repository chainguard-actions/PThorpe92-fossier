<!-- markdownlint-disable -->

# Hardening Report: PThorpe92--fossier/v0.0.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **PThorpe92--fossier/v0.0.1** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Three `uses:` references in action.yml are pinned to mutable tags rather than immutable 40-character SHA digests, making the action vulnerable to supply-chain attacks if those tags are moved:
- `astral-sh/setup-uv@v4` (line 43)
- `actions/setup-python@v5` (line 46)
- `actions/cache@v4` (line 55)
Each should be pinned to a full commit SHA, e.g. `actions/cache@1bd1e32a3bdc45362d1e726936510720a7c6158d # v4`.

Locations:

- `action.yml:43`
- `action.yml:46`
- `action.yml:55`

### script-injection (severity: high)

Sub-rule (a): A `${{ ... }}` expression is interpolated directly inside a `run:` shell command string. The step 'Install fossier' contains:

  run: uv pip install --system ${{ github.action_path }}

Any `${{ ... }}` expression directly in a `run:` block is a script-injection risk because the expression value is substituted into the shell command string before the shell parses it. The safe alternative is to pass the value via an `env:` variable and reference it as a quoted shell variable, e.g.:

  env:
    ACTION_PATH: ${{ github.action_path }}
  run: uv pip install --system "$ACTION_PATH"

Locations:

- `action.yml:51`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Fixed all three unpinned `uses:` references by resolving their full commit SHAs: `astral-sh/setup-uv@v4` → `@38f3f104447c67c051c4a08e39b64a148898af3a # v4`, `actions/setup-python@v5` → `@a26af69be951a213d495a4c3e4e4022e16d87065 # v5`, `actions/cache@v4` → `@0057852bfaa89a56745cba8c7296529d2fc39830 # v4`. Fixed script-injection in the 'Install fossier' step by moving `${{ github.action_path }}` into an `env:` block as `ACTION_PATH` and referencing it as `"$ACTION_PATH"` in the shell command.

