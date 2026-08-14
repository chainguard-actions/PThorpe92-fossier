<!-- markdownlint-disable -->

# Hardening Report: PThorpe92--fossier/v0.0.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **PThorpe92--fossier/v0.0.1** was hardened automatically. 2 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): The 'Install fossier' step directly interpolates a ${{ }} expression inside a run: shell command string. The line `run: uv pip install --system ${{ github.action_path }}` embeds the GitHub Actions expression directly in the shell command before the shell ever sees it. Any ${{ ... }} expression inside a run: block is a script-injection finding regardless of which context it reads from.

Locations:

- `action.yml:52`

### unpinned-uses (severity: high)

Three uses: references in action.yml use mutable tag refs instead of pinned 40-character SHA commit hashes, making the action vulnerable to supply-chain attacks if those tags are moved: `uses: astral-sh/setup-uv@v4` (line 44), `uses: actions/setup-python@v5` (line 47), `uses: actions/cache@v4` (line 55).

Locations:

- `action.yml:44`
- `action.yml:47`
- `action.yml:55`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses

**Notes:**

Fixed script-injection on line 52 by moving `${{ github.action_path }}` into an env var (ACTION_PATH) and referencing it as "$ACTION_PATH" in the shell run command. Pinned all three unpinned action references to full 40-character commit SHAs: astral-sh/setup-uv@v4 → 38f3f104447c67c051c4a08e39b64a148898af3a, actions/setup-python@v5 → a26af69be951a213d495a4c3e4e4022e16d87065, actions/cache@v4 → 0057852bfaa89a56745cba8c7296529d2fc39830. Original tag names preserved as inline comments.

### Iteration 2

**Fixes applied:** unpinned-uses, script-injection, missing-permissions

**Notes:**

1. unpinned-uses: Pinned all 7 action references to full SHAs with tag comments:
   - actions/checkout@v4 → @34e114876b0b11c390a56381ad16ebd13914f8d5 (ci.yml, fossier-scan.yml, fossier.yml)
   - astral-sh/setup-uv@v4 → @38f3f104447c67c051c4a08e39b64a148898af3a (ci.yml, fossier-scan.yml)
   - actions/setup-python@v5 → @a26af69be951a213d495a4c3e4e4022e16d87065 (fossier-scan.yml)
   - actions/cache@v4 → @0057852bfaa89a56745cba8c7296529d2fc39830 (fossier-scan.yml)
2. script-injection: Moved `${{ inputs.dry-run }}` out of the run: shell string in fossier-scan.yml into an env var `DRY_RUN`, referenced as `$DRY_RUN` in the shell script.
3. missing-permissions: Added `permissions: contents: read` top-level block to ci.yml (the workflow only needs to read code to run tests).

