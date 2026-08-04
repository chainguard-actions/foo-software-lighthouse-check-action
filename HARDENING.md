<!-- markdownlint-disable -->

# Hardening Report: foo-software--lighthouse-check-action/v12.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **foo-software--lighthouse-check-action/v12.0.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference GitHub Actions using mutable branch refs (@master) instead of pinned 40-character commit SHAs. This exposes the workflow to supply-chain attacks if the referenced branch is compromised. Affected references: actions/checkout@master, amondnet/vercel-action@master, actions/upload-artifact@master (and local ./ references).

Locations:

- `.github/workflows/comment-on-push.yml:12`
- `.github/workflows/extraHeaders.yml:10`
- `.github/workflows/extraHeaders.yml:12`
- `.github/workflows/extraHeaders.yml:25`
- `.github/workflows/foo-api.yml:8`
- `.github/workflows/overrides.yml:8`
- `.github/workflows/test.yml:8`
- `.github/workflows/test.yml:26`

### missing-permissions (severity: medium)

None of the workflow files define a top-level `permissions:` key, and no job within them defines job-level permissions. Without explicit permissions, workflows run with the default (potentially broad) token permissions, violating the principle of least privilege.

Locations:

- `.github/workflows/comment-on-push.yml:1`
- `.github/workflows/extraHeaders.yml:1`
- `.github/workflows/foo-api.yml:1`
- `.github/workflows/overrides.yml:1`
- `.github/workflows/test.yml:1`

### script-injection (severity: high)

Sub-rule (a): Two workflow `run:` steps directly interpolate a `${{ ... }}` expression inside a shell command string. Although `github.workspace` is not attacker-controlled in the same way as `github.head_ref`, any `${{ ... }}` expression inside a `run:` block is a script-injection finding because the value is substituted by the YAML template engine before the shell ever sees it. Offending lines: `run: mkdir -p ${{ github.workspace }}/tmp/artifacts`

Locations:

- `.github/workflows/extraHeaders.yml:11`
- `.github/workflows/test.yml:9`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, script-injection

**Notes:**

Fixed all 5 workflow files: (1) Pinned all @master action references to full 40-char SHAs: actions/checkout→61b9e3751b92087fd0b06925ba6dd6314e06f089, amondnet/vercel-action→9471a567336164bdfd4c8f070039943d597f6f5c, actions/upload-artifact→0c366cb4fc8897159c94880f94b55bc716ad6a66. (2) Added `permissions: {}` top-level block to all 5 workflow files. (3) Fixed script injection in extraHeaders.yml and test.yml by moving `${{ github.workspace }}` out of run: shell strings into step-level env: blocks (GITHUB_WORKSPACE_PATH), then referencing via the env var in the shell command.

