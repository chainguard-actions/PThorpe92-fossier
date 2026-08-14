<!-- markdownlint-disable -->

# Hardening Report: PThorpe92--fossier/v0.0.4

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **PThorpe92--fossier/v0.0.4** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple `uses:` references are pinned to mutable tags instead of immutable 40-character commit SHAs, making the action vulnerable to supply-chain attacks if the tag is moved.

**action.yml:**
- `astral-sh/setup-uv@v4`
- `actions/setup-python@v5`
- `actions/cache@v4`

**ci.yml:**
- `actions/checkout@v4`
- `astral-sh/setup-uv@v4`

**fossier-scan.yml:**
- `actions/checkout@v4`
- `astral-sh/setup-uv@v4`
- `actions/setup-python@v5`
- `actions/cache@v4`

**fossier.yml:**
- `actions/checkout@v4`

Locations:

- `action.yml:49`
- `action.yml:52`
- `action.yml:60`
- `.github/workflows/ci.yml:13`
- `.github/workflows/ci.yml:16`
- `.github/workflows/fossier-scan.yml:20`
- `.github/workflows/fossier-scan.yml:23`
- `.github/workflows/fossier-scan.yml:26`
- `.github/workflows/fossier-scan.yml:32`
- `.github/workflows/fossier.yml:14`

### script-injection (severity: high)

GitHub Actions expressions (`${{ ... }}`) are interpolated directly inside `run:` shell command strings, violating sub-rule (a). Before the shell executes the command, the YAML template engine substitutes the expression value verbatim, allowing an attacker-controlled value to inject arbitrary shell commands.

**action.yml** — `Install fossier` step: `run: uv pip install --system ${{ github.action_path }}`. The `github.action_path` context is substituted directly into the shell command without quoting or env-var indirection.

**fossier-scan.yml** — `Scan open PRs` step: `if [ "${{ inputs.dry-run }}" = "true" ]`. The `inputs.dry-run` value (supplied by a `workflow_dispatch` caller) is interpolated directly into the shell `if` expression.

Locations:

- `action.yml:57`
- `.github/workflows/fossier-scan.yml:44`

### missing-permissions (severity: medium)

`.github/workflows/ci.yml` has no top-level `permissions:` key and its only job (`test`) also has no job-level `permissions:` key. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad (e.g. `write` access to contents and pull-requests). A minimal `permissions: read-all` or specific scopes should be declared.

Locations:

- `.github/workflows/ci.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, missing-permissions

**Notes:**

Fixed all three findings: (1) Pinned all mutable tag references to full 40-char commit SHAs in action.yml, ci.yml, fossier-scan.yml, and fossier.yml using lookup_action_sha for astral-sh/setup-uv@v4, actions/setup-python@v5, actions/cache@v4, and actions/checkout@v4. (2) Fixed script injection in action.yml by moving github.action_path into an ACTION_PATH env var, and in fossier-scan.yml by moving inputs.dry-run into a DRY_RUN env var. (3) Added top-level `permissions: contents: read` to ci.yml which had no permissions block.

