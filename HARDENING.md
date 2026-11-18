<!-- markdownlint-disable -->

# Hardening Report: PThorpe92--fossier/v0.0.5

> This file was generated automatically by the hardening agent.

**Policy SHA:** `374911c4be584df24e6da57af2d990570ef0f859`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **PThorpe92--fossier/v0.0.5** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml references actions by mutable tag instead of a full 40-char commit SHA: 'uses: astral-sh/setup-uv@v4', 'uses: actions/setup-python@v5', 'uses: actions/cache@v4'.

Locations:

- `action.yml:54`
- `action.yml:57`
- `action.yml:66`

### script-injection (severity: high)

Rule (a): the 'Install fossier' step puts a GitHub Actions expression directly inside the run: script: 'run: uv pip install --system ${{ github.action_path }}'. The value is pasted into the script before the shell parses it. Pass it through env (e.g. ACTION_PATH: ${{ github.action_path }}) and use "$ACTION_PATH" in double quotes, or use the runner-provided "$GITHUB_ACTION_PATH".

Locations:

- `action.yml:63`

### prior-fix (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. The previous harden changed action.yml without recording a finding for it, and this analysis reported nothing there. Re-apply its security changes unless the code no longer needs them. The previous patch:
```diff
diff --git a/action.yml b/action.yml
index eb59504..6948c8f 100644
--- a/action.yml
+++ b/action.yml
@@ -3,7 +3,6 @@ description: "Evaluate pull request contributors for spam probability"
 branding:
   icon: "shield"
   color: "orange"
-
 inputs:
   github-token:
     description: "GitHub token with PR write access"
@@ -32,7 +31,6 @@ inputs:
     description: "Custom registry URL (default: https://registry.fossier.io)"
     required: false
     default: ""
-
 outputs:
   outcome:
     description: "Decision outcome: allow, review, or deny"
@@ -46,30 +44,44 @@ outputs:
   details:
     description: "Full decision details as JSON"
     value: ${{ steps.evaluate.outputs.details }}
-
 runs:
   using: "composite"
   steps:
     - name: Set up uv
       uses: astral-sh/setup-uv@v4
-
     - name: Set up Python
       uses: actions/setup-python@v5
       with:
         python-version: "3.13"
-
     - name: Install fossier
       shell: bash
-      run: uv pip install --system ${{ github.action_path }}
-
+      env:
+        ACTION_PATH: ${{ github.action_path }}
+      run: uv pip install --system "$ACTION_PATH"
     - name: Restore DB cache
       uses: actions/cache@v4
       with:
         path: .fossier.db
         key: ${{ inputs.db-cache-key }}-${{ github.repository }}
         restore-keys: |
           ${{ inputs.db-cache-key }}-${{ github.repository }}
-
     - name: Evaluate PR
       id: evaluate
       shell: bash
@@ -89,7 +101,6 @@ runs:
           exit 1
         fi
         exit 0
-
     - name: Open trust update PR
       if: env.FOSSIER_TRUST_CHANGED == 'true'
       shell: bash

```

Locations:

- `action.yml`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, prior-fix

**Notes:**

Pinned astral-sh/setup-uv, actions/setup-python, actions/cache to full SHAs (tag comments kept). Moved github.action_path into env ACTION_PATH and quoted it in the uv install step, re-applying the previous harden's security change.

