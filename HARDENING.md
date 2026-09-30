<!-- markdownlint-disable -->

# Hardening Report: PThorpe92--fossier/v0.0.5

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **PThorpe92--fossier/v0.0.5** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): A ${{ }} expression is directly interpolated inside a run: shell command string. The step 'Install fossier' contains: `run: uv pip install --system ${{ github.action_path }}`. Even though github.action_path is not directly attacker-controlled, any ${{ ... }} expression inside a run: block is a script-injection finding because the value flows through YAML template substitution before the shell ever sees it, bypassing shell quoting. The value should be passed via an env: variable instead (e.g., `env: ACTION_PATH: ${{ github.action_path }}` and then `run: uv pip install --system "$ACTION_PATH"`).

Locations:

- `action.yml:57`

### unpinned-uses (severity: high)

Three uses: references in action.yml are pinned to mutable version tags rather than immutable 40-character commit SHAs, making the action vulnerable to supply-chain attacks if any of those tags are moved or compromised. Failing references: (1) `uses: astral-sh/setup-uv@v4` — should be pinned to a full SHA; (2) `uses: actions/setup-python@v5` — should be pinned to a full SHA; (3) `uses: actions/cache@v4` — should be pinned to a full SHA.

Locations:

- `action.yml:48`
- `action.yml:51`
- `action.yml:60`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Fixed all three unpinned `uses:` references by pinning them to their full 40-character commit SHAs (astral-sh/setup-uv@38f3f10, actions/setup-python@a26af69, actions/cache@0057852), preserving the version tag in a comment. Fixed the script-injection finding in the 'Install fossier' step by moving `${{ github.action_path }}` into an `env:` block as `ACTION_PATH` and referencing it as `"$ACTION_PATH"` in the shell command.

