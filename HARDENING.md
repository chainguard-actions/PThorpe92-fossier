<!-- markdownlint-disable -->

# Hardening Report: PThorpe92--fossier/v0.0.5

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **PThorpe92--fossier/v0.0.5** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Three `uses:` references in action.yml are pinned to mutable tags rather than full 40-character commit SHAs, making the action vulnerable to supply-chain attacks if those tags are moved: `astral-sh/setup-uv@v4` (line 54), `actions/setup-python@v5` (line 57), `actions/cache@v4` (line 66). Each should be pinned to a full SHA digest, e.g. `astral-sh/setup-uv@<40-hex-sha> # v4`.

Locations:

- `action.yml:54`
- `action.yml:57`
- `action.yml:66`

### script-injection (severity: high)

Sub-rule (a): The 'Install fossier' step directly interpolates a `${{ ... }}` expression inside a `run:` shell command string: `run: uv pip install --system ${{ github.action_path }}`. YAML template substitution occurs before the shell sees the command, so any `${{ ... }}` directly inside a `run:` block is a script-injection risk. The value should instead be passed via an `env:` variable (e.g. `ACTION_PATH: ${{ github.action_path }}`) and referenced as `"$ACTION_PATH"` in the shell script.

Locations:

- `action.yml:63`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Fixed three unpinned `uses:` references by pinning them to full 40-character commit SHAs: `astral-sh/setup-uv@v4` → `38f3f104447c67c051c4a08e39b64a148898af3a`, `actions/setup-python@v5` → `a26af69be951a213d495a4c3e4e4022e16d87065`, `actions/cache@v4` → `0057852bfaa89a56745cba8c7296529d2fc39830`. Fixed script injection in the 'Install fossier' step by moving `${{ github.action_path }}` into an `env:` block as `ACTION_PATH` and referencing it as `"$ACTION_PATH"` in the shell command.

