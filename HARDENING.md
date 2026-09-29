<!-- markdownlint-disable -->

# Hardening Report: PThorpe92--fossier/v0.0.5

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **PThorpe92--fossier/v0.0.5** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Three `uses:` references in action.yml use mutable version tags instead of pinned 40-character commit SHAs, making the action vulnerable to supply-chain attacks if those tags are moved:
- `astral-sh/setup-uv@v4` (line 54)
- `actions/setup-python@v5` (line 57)
- `actions/cache@v4` (line 66)
Each should be pinned to a full SHA digest, e.g. `actions/checkout@<40-hex-char-sha> # v4`.

Locations:

- `action.yml:54`
- `action.yml:57`
- `action.yml:66`

### script-injection (severity: high)

Sub-rule (a): A `${{ ... }}` expression is interpolated directly inside a `run:` shell command string. In the "Install fossier" step, `${{ github.action_path }}` is embedded directly in the shell command:

  run: uv pip install --system ${{ github.action_path }}

GitHub Actions performs YAML template substitution before the shell ever sees the string, so the value is inserted unquoted into the shell command. Even though `github.action_path` is not directly attacker-controlled, any `${{ ... }}` expression inside a `run:` block is a script-injection finding per the check rules. The fix is to pass the value via an `env:` variable and reference it as a quoted shell variable: `run: uv pip install --system "$ACTION_PATH"` with `env: ACTION_PATH: ${{ github.action_path }}`.

Locations:

- `action.yml:63`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Pinned all three unpinned action references to full 40-character commit SHAs (astral-sh/setup-uv@v4→38f3f104..., actions/setup-python@v5→a26af69b..., actions/cache@v4→00578528...) with tag comments for readability. Fixed script injection in the 'Install fossier' step by moving ${{ github.action_path }} into an env: block as ACTION_PATH and referencing it as "$ACTION_PATH" in the run: command.

