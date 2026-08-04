<!-- markdownlint-disable -->

# Hardening Report: foo-software--lighthouse-check-action/v9.1.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **foo-software--lighthouse-check-action/v9.1.1** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference actions using mutable branch/tag refs (@master) instead of pinned 40-character commit SHAs. This exposes the workflow to supply-chain attacks if the referenced action is compromised or modified. Failing references:
- .github/workflows/test.yml: `actions/checkout@master` (line 7), `actions/upload-artifact@master` (line 24)
- .github/workflows/comment-on-push.yml: `actions/checkout@master` (line 8)
- .github/workflows/extraHeaders.yml: `actions/checkout@master` (line 10), `amondnet/vercel-action@master` (line 11), `actions/upload-artifact@master` (line 23)
- .github/workflows/foo-api.yml: `actions/checkout@master` (line 8)
- .github/workflows/overrides.yml: `actions/checkout@master` (line 6)

Locations:

- `.github/workflows/test.yml:7`
- `.github/workflows/test.yml:24`
- `.github/workflows/comment-on-push.yml:8`
- `.github/workflows/extraHeaders.yml:10`
- `.github/workflows/extraHeaders.yml:11`
- `.github/workflows/extraHeaders.yml:23`
- `.github/workflows/foo-api.yml:8`
- `.github/workflows/overrides.yml:6`

### script-injection (severity: high)

Rule (a): A `${{ ... }}` expression is interpolated directly inside a `run:` shell command string. Even though `github.workspace` is GitHub-controlled, injecting any expression directly into a shell command via YAML template substitution bypasses shell quoting and is a script-injection risk. Offending lines:
- test.yml line 8: `run: mkdir -p ${{ github.workspace }}/tmp/artifacts`
- extraHeaders.yml line 11: `run: mkdir -p ${{ github.workspace }}/tmp/artifacts`
Fix: use the environment variable `$GITHUB_WORKSPACE` instead of the expression `${{ github.workspace }}`.

Locations:

- `.github/workflows/test.yml:8`
- `.github/workflows/extraHeaders.yml:11`

### missing-permissions (severity: medium)

None of the workflow files define a top-level `permissions:` key, and no individual job defines a `permissions:` block. Without explicit permissions, workflows run with the default (potentially broad) token permissions, violating the principle of least privilege. All five workflow files are affected: test.yml, comment-on-push.yml, extraHeaders.yml, foo-api.yml, and overrides.yml.

Locations:

- `.github/workflows/test.yml:1`
- `.github/workflows/comment-on-push.yml:1`
- `.github/workflows/extraHeaders.yml:1`
- `.github/workflows/foo-api.yml:1`
- `.github/workflows/overrides.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, missing-permissions

**Notes:**

Fixed all three findings across five workflow files: (1) Pinned all @master action references to full commit SHAs: actions/checkout@61b9e3751b92087fd0b06925ba6dd6314e06f089, actions/upload-artifact@0c366cb4fc8897159c94880f94b55bc716ad6a66, amondnet/vercel-action@9471a567336164bdfd4c8f070039943d597f6f5c. (2) Replaced ${{ github.workspace }} expressions in run: steps with the $GITHUB_WORKSPACE environment variable in test.yml and extraHeaders.yml. (3) Added permissions: {} top-level block to all five workflow files to enforce least-privilege token permissions.

