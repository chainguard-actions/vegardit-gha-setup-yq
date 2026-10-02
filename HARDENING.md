<!-- markdownlint-disable -->

# Hardening Report: vegardit--gha-setup-yq/1.1.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **vegardit--gha-setup-yq/1.1.1** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Two `uses:` references in action.yml pin to a mutable version tag (`@v5`) rather than a full 40-character commit SHA. This means the action can silently pull in changed code on future runs, enabling supply-chain attacks.

Failing references:
- `uses: actions/cache/restore@v5` (line 39)
- `uses: actions/cache/save@v5` (line 183)

These should be pinned to their full SHA digests, e.g.:
  `uses: actions/cache/restore@<40-char-sha> # v5`

Locations:

- `action.yml:39`
- `action.yml:183`

### github-env-injection (severity: high)

The `run:` block in the 'yq: setup' step writes the inherited process env var `$RUNNER_TEMP` to `$GITHUB_PATH` without applying the required sanitization step (`printf '%s' ... | tr -d '\n\r'`). In a composite action, `$RUNNER_TEMP` is an inherited env var not set within the same `run:` block — it is controlled by the calling workflow environment and must be treated as untrusted. A calling workflow could set `RUNNER_TEMP` to a value containing newlines, allowing injection of arbitrary entries into `$GITHUB_PATH`.

Offending line (~line 157):
  `echo "$RUNNER_TEMP/yq" >> "$GITHUB_PATH"`

Correct pattern:
  `safe=$(printf '%s' "$RUNNER_TEMP" | tr -d '\n\r')`
  `echo "${safe}/yq" >> "$GITHUB_PATH"`

Locations:

- `action.yml:157`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, github-env-injection

**Notes:**

Fixed three issues in hardened/action/action.yml: (1) Pinned `actions/cache/restore@v5` to full SHA `caa296126883cff596d87d8935842f9db880ef25 # v5`; (2) Pinned `actions/cache/save@v5` to the same full SHA `caa296126883cff596d87d8935842f9db880ef25 # v5`; (3) Sanitized `$RUNNER_TEMP` before writing to `$GITHUB_PATH` by using `safe_runner_temp=$(printf '%s' "$RUNNER_TEMP" | tr -d '\n\r')` and then `echo "${safe_runner_temp}/yq" >> "$GITHUB_PATH"` to prevent newline injection.

