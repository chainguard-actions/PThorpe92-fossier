<!-- markdownlint-disable -->

# Hardening Report: PThorpe92--fossier/v0.0.4

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **PThorpe92--fossier/v0.0.4** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Three `uses:` references in action.yml use mutable version tags instead of pinned 40-character SHA commit hashes, making the action vulnerable to supply-chain attacks if the referenced tags are moved or overwritten:
- `astral-sh/setup-uv@v4`
- `actions/setup-python@v5`
- `actions/cache@v4`
Each should be pinned to a full SHA, e.g. `actions/cache@1bd1e32a3bdc45362d1e726936510720a7c6158d # v4`.

Locations:

- `action.yml:48`
- `action.yml:51`
- `action.yml:58`

### script-injection (severity: high)

Sub-rule (a): The 'Install fossier' step directly interpolates a `${{ }}` expression inside a `run:` shell command string: `run: uv pip install --system ${{ github.action_path }}`. Any `${{ ... }}` expression inside a `run:` block is subject to YAML template substitution before the shell processes it, creating a script-injection risk. The value should be passed via an `env:` variable and referenced as `$ACTION_PATH` (double-quoted) instead.

Locations:

- `action.yml:55`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Fixed all three unpinned `uses:` references by pinning them to their full 40-character SHA commit hashes with tag comments: `astral-sh/setup-uv@38f3f104447c67c051c4a08e39b64a148898af3a # v4`, `actions/setup-python@a26af69be951a213d495a4c3e4e4022e16d87065 # v5`, and `actions/cache@0057852bfaa89a56745cba8c7296529d2fc39830 # v4`. Fixed the script injection in the 'Install fossier' step by moving `${{ github.action_path }}` into an `env:` block as `ACTION_PATH` and referencing it as `"$ACTION_PATH"` in the shell command.

