<!-- markdownlint-disable -->

# Hardening Report: PThorpe92--fossier/v0.0.5

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **PThorpe92--fossier/v0.0.5** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple `uses:` references are pinned to mutable version tags rather than immutable 40-character SHA digests, making the action vulnerable to supply-chain attacks if the upstream tag is moved or compromised.

action.yml:
  - uses: astral-sh/setup-uv@v4
  - uses: actions/setup-python@v5
  - uses: actions/cache@v4

.github/workflows/ci.yml:
  - uses: actions/checkout@v4
  - uses: astral-sh/setup-uv@v4

.github/workflows/fossier-scan.yml:
  - uses: actions/checkout@v4
  - uses: astral-sh/setup-uv@v4
  - uses: actions/setup-python@v5
  - uses: actions/cache@v4

.github/workflows/fossier.yml:
  - uses: actions/checkout@v4

Locations:

- `action.yml:53`
- `action.yml:56`
- `action.yml:63`
- `.github/workflows/ci.yml:12`
- `.github/workflows/ci.yml:15`
- `.github/workflows/fossier-scan.yml:19`
- `.github/workflows/fossier-scan.yml:22`
- `.github/workflows/fossier-scan.yml:25`
- `.github/workflows/fossier-scan.yml:33`
- `.github/workflows/fossier.yml:17`

### script-injection (severity: high)

Sub-rule (a): GitHub Actions expressions are interpolated directly inside `run:` shell command strings, bypassing shell quoting and allowing injection of arbitrary shell metacharacters.

1. action.yml, 'Install fossier' step: `run: uv pip install --system ${{ github.action_path }}` — the `${{ github.action_path }}` expression is expanded by the Actions runner into the shell command before the shell parses it. Although `github.action_path` is runner-controlled, any `${{ ... }}` directly in a `run:` block is a script-injection finding per the check rules. Fix: use the `$GITHUB_ACTION_PATH` environment variable instead.

2. .github/workflows/fossier-scan.yml, 'Scan open PRs' step: `if [ "${{ inputs.dry-run }}" = "true" ]` — the `${{ inputs.dry-run }}` expression (a workflow_dispatch boolean input) is interpolated directly into the shell command. An attacker with workflow_dispatch access could supply a crafted value. Fix: route through an `env:` variable and reference `"$DRY_RUN"` in the shell.

Locations:

- `action.yml:60`
- `.github/workflows/fossier-scan.yml:41`

### missing-permissions (severity: medium)

.github/workflows/ci.yml has no top-level `permissions:` key and its only job (`test`) also has no job-level `permissions:` key. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be broader than necessary (e.g., write access to contents and pull-requests). A minimal permissions block such as `permissions: read-all` or specific scopes (e.g., `contents: read`) should be added.

Locations:

- `.github/workflows/ci.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, missing-permissions

**Notes:**

1. Pinned all `uses:` references to full 40-char SHAs: actions/checkout@34e114876b0b11c390a56381ad16ebd13914f8d5 (v4), astral-sh/setup-uv@38f3f104447c67c051c4a08e39b64a148898af3a (v4), actions/setup-python@a26af69be951a213d495a4c3e4e4022e16d87065 (v5), actions/cache@0057852bfaa89a56745cba8c7296529d2fc39830 (v4) — applied across action.yml, ci.yml, fossier-scan.yml, and fossier.yml. 2. Fixed script injection in action.yml by replacing `${{ github.action_path }}` with `$GITHUB_ACTION_PATH` env var in the 'Install fossier' run step. Fixed script injection in fossier-scan.yml by moving `${{ inputs.dry-run }}` into an `env:` block as `DRY_RUN` and referencing `$DRY_RUN` in the shell. 3. Added `permissions: contents: read` top-level block to ci.yml.

