<!-- markdownlint-disable -->

# Hardening Report: PThorpe92--fossier/v0.0.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **PThorpe92--fossier/v0.0.1** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): A GitHub Actions expression `${{ github.action_path }}` is directly interpolated inside a `run:` shell command string in the 'Install fossier' step. Any `${{ ... }}` expression inside a `run:` block is a script-injection risk because the value is substituted into the shell command before the shell parses it. Offending line: `run: uv pip install --system ${{ github.action_path }}`

Locations:

- `action.yml:51`

### unpinned-uses (severity: high)

Three `uses:` references in action.yml use mutable version tags instead of pinned 40-character SHA digests, making the action vulnerable to supply-chain attacks if those tags are moved or hijacked:
- `astral-sh/setup-uv@v4` (line 43)
- `actions/setup-python@v5` (line 45)
- `actions/cache@v4` (line 48)
All should be pinned to their full commit SHA.

Locations:

- `action.yml:43`
- `action.yml:45`
- `action.yml:48`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses

**Notes:**

Fixed script-injection by moving `${{ github.action_path }}` into an `env:` block as `ACTION_PATH` and referencing it as `"$ACTION_PATH"` in the run shell command. Pinned all three unpinned uses references to their full commit SHAs: astral-sh/setup-uv@38f3f104447c67c051c4a08e39b64a148898af3a (v4), actions/setup-python@a26af69be951a213d495a4c3e4e4022e16d87065 (v5), actions/cache@0057852bfaa89a56745cba8c7296529d2fc39830 (v4).

