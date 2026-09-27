<!-- markdownlint-disable -->

# Hardening Report: PThorpe92--fossier/v0.0.4

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **PThorpe92--fossier/v0.0.4** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Three `uses:` references in action.yml are pinned to mutable version tags instead of immutable 40-character SHA digests. If any of these tags are moved or the upstream repository is compromised, the action will silently execute attacker-controlled code. Failing references: `astral-sh/setup-uv@v4` (line 49), `actions/setup-python@v5` (line 53), `actions/cache@v4` (line 61).

Locations:

- `action.yml:49`
- `action.yml:53`
- `action.yml:61`

### script-injection (severity: high)

Sub-rule (a): The 'Install fossier' run: block directly interpolates a GitHub Actions expression inside the shell command string: `run: uv pip install --system ${{ github.action_path }}`. Any `${{ ... }}` expression interpolated directly into a run: script is a script-injection risk because the value is substituted into the shell command before the shell parses it, bypassing shell quoting. Fix by passing the value via an env: variable and referencing it as a quoted shell variable: `env: ACTION_PATH: ${{ github.action_path }}` then `run: uv pip install --system "$ACTION_PATH"`.

Locations:

- `action.yml:57`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Fixed all three unpinned `uses:` references by resolving their mutable version tags to immutable 40-character SHA digests (astral-sh/setup-uv@v4 → 38f3f104..., actions/setup-python@v5 → a26af69b..., actions/cache@v4 → 0057852b...). Fixed the script-injection finding in the 'Install fossier' step by moving `${{ github.action_path }}` into an `env:` block as `ACTION_PATH` and referencing it as `"$ACTION_PATH"` in the shell command.

