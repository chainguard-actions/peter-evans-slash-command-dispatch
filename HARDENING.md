<!-- markdownlint-disable -->

# Hardening Report: peter-evans--slash-command-dispatch/v5.0.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **peter-evans--slash-command-dispatch/v5.0.2** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Direct expression interpolation of user-controlled workflow_dispatch inputs inside run: shell commands. In update-major-version.yml, `${{ github.event.inputs.main_version }}` and `${{ github.event.inputs.target }}` are interpolated directly into git commands without any quoting or sanitization. An attacker with workflow_dispatch access could inject arbitrary shell commands via these inputs.

Locations:

- `.github/workflows/update-major-version.yml:28`
- `.github/workflows/update-major-version.yml:30`

### unpinned-uses (severity: high)

Multiple workflow files reference external actions using mutable version tags instead of full 40-character commit SHAs, making them vulnerable to supply-chain attacks if the tag is moved. Failing references: automerge-dependabot.yml: `peter-evans/enable-pull-request-automerge@v3`; ci.yml: `actions/checkout@v6`, `actions/setup-node@v6`, `actions/upload-artifact@v5`, `actions/download-artifact@v6`, `peter-evans/create-pull-request@v7`; hello-world-command.yml: `peter-evans/create-or-update-comment@v5` (×2); ping-command.yml: `peter-evans/create-or-update-comment@v5`; slash-command-dispatch.yml: `actions/checkout@v6`, `peter-evans/create-or-update-comment@v5`; update-major-version.yml: `actions/checkout@v6`.

Locations:

- `.github/workflows/automerge-dependabot.yml:8`
- `.github/workflows/ci.yml:16`
- `.github/workflows/ci.yml:17`
- `.github/workflows/ci.yml:23`
- `.github/workflows/ci.yml:33`
- `.github/workflows/ci.yml:36`
- `.github/workflows/hello-world-command.yml:9`
- `.github/workflows/hello-world-command.yml:20`
- `.github/workflows/ping-command.yml:9`
- `.github/workflows/slash-command-dispatch.yml:13`
- `.github/workflows/slash-command-dispatch.yml:97`
- `.github/workflows/update-major-version.yml:19`

### missing-permissions (severity: medium)

None of the workflow files define a top-level `permissions:` block, and no job within any workflow defines job-level permissions. Without explicit permissions, workflows run with the default (often broad) token permissions, violating the principle of least privilege. Affected files: automerge-dependabot.yml, ci.yml, hello-world-command.yml, ping-command.yml, slash-command-dispatch.yml, update-major-version.yml.

Locations:

- `.github/workflows/automerge-dependabot.yml:1`
- `.github/workflows/ci.yml:1`
- `.github/workflows/hello-world-command.yml:1`
- `.github/workflows/ping-command.yml:1`
- `.github/workflows/slash-command-dispatch.yml:1`
- `.github/workflows/update-major-version.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed all three findings across six workflow files:

1. script-injection (update-major-version.yml lines 28, 30): Moved `${{ github.event.inputs.main_version }}` and `${{ github.event.inputs.target }}` from inline shell interpolation into step env: blocks, referenced as double-quoted `"$MAIN_VERSION"` and `"$TARGET"` in the run: commands.

2. unpinned-uses (12 locations): Pinned all action references to full 40-char commit SHAs — actions/checkout@d23441a4, actions/setup-node@249970729, actions/upload-artifact@330a01c4, actions/download-artifact@018cc2cf, peter-evans/create-pull-request@22a90890, peter-evans/create-or-update-comment@e8674b07, peter-evans/enable-pull-request-automerge@a660677d — with original tag preserved in a comment.

3. missing-permissions (6 files): Added top-level `permissions:` blocks with least-privilege grants: automerge-dependabot.yml (pull-requests: write), ci.yml (contents: read top-level; package job gets contents: write + pull-requests: write), hello-world-command.yml (issues: write), ping-command.yml (issues: write), slash-command-dispatch.yml (issues: write + pull-requests: write), update-major-version.yml (contents: write).

