<!-- markdownlint-disable -->

# Hardening Report: PThorpe92--fossier/v0.0.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **PThorpe92--fossier/v0.0.3** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Three `uses:` references in action.yml are pinned to mutable version tags instead of immutable 40-character commit SHAs, making the action vulnerable to supply-chain attacks if those tags are moved:
- `astral-sh/setup-uv@v4` (line 51)
- `actions/setup-python@v5` (line 54)
- `actions/cache@v4` (line 61)
Each should be replaced with a full SHA digest, e.g. `actions/cache@<40-hex-sha> # v4`.

Locations:

- `action.yml:51`
- `action.yml:54`
- `action.yml:61`

### script-injection (severity: high)

Sub-rule (a): The 'Install fossier' step directly interpolates a `${{ ... }}` expression inside a `run:` shell command string: `run: uv pip install --system ${{ github.action_path }}`. Any `${{ ... }}` expression interpolated directly into a `run:` block is a script-injection risk because the value is substituted into the shell command string before the shell parses it. This should be replaced with the equivalent environment variable `$GITHUB_ACTION_PATH` (which GitHub Actions sets automatically), eliminating the template interpolation entirely.

Locations:

- `action.yml:55`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Fixed all three unpinned `uses:` references by replacing mutable version tags with full 40-character commit SHAs (astral-sh/setup-uv@v4 → 38f3f104..., actions/setup-python@v5 → a26af69b..., actions/cache@v4 → 00578528...). Fixed the script injection on line 55 by replacing `${{ github.action_path }}` with the built-in `$GITHUB_ACTION_PATH` environment variable that GitHub Actions sets automatically, eliminating the template interpolation risk entirely.

