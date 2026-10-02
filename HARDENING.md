<!-- markdownlint-disable -->

# Hardening Report: vegardit--gha-setup-yq/1.1.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **vegardit--gha-setup-yq/1.1.1** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Two `uses:` references in action.yml use mutable version tags (@v5) instead of pinned 40-character SHA commit digests. This exposes the action to supply-chain attacks if the upstream action tag is moved or compromised.

Failing references:
- `uses: actions/cache/restore@v5` (line 34)
- `uses: actions/cache/save@v5` (line 175)

These should be pinned to their full SHA digests, e.g. `uses: actions/cache/restore@<40-char-sha> # v5`.

Locations:

- `action.yml:34`
- `action.yml:175`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned both unpinned `uses:` references in hardened/action/action.yml to their full SHA digests:
- `actions/cache/restore@v5` → `actions/cache/restore@caa296126883cff596d87d8935842f9db880ef25 # v5` (line 34)
- `actions/cache/save@v5` → `actions/cache/save@caa296126883cff596d87d8935842f9db880ef25 # v5` (line 175)
Both resolve to the same SHA as they are sub-actions of the same `actions/cache` repository at the v5 tag.

