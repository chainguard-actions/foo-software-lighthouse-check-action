<!-- markdownlint-disable -->

# Hardening Report: foo-software--lighthouse-check-action/v12.0.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **foo-software--lighthouse-check-action/v12.0.1** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All `uses:` references across all workflow files are pinned to the `@master` branch tag instead of a full 40-character commit SHA. This means a compromised or force-pushed upstream action could silently change the code executed in CI. Affected references include: `actions/checkout@master`, `actions/upload-artifact@master`, and `amondnet/vercel-action@master`.

Locations:

- `.github/workflows/test.yml:7`
- `.github/workflows/test.yml:24`
- `.github/workflows/comment-on-push.yml:9`
- `.github/workflows/extraHeaders.yml:10`
- `.github/workflows/extraHeaders.yml:13`
- `.github/workflows/extraHeaders.yml:27`
- `.github/workflows/foo-api.yml:9`
- `.github/workflows/overrides.yml:8`

### script-injection (severity: high)

Rule (a): A `${{ ... }}` expression is interpolated directly inside a `run:` shell command string. Specifically, `${{ github.workspace }}` is embedded in `run: mkdir -p ${{ github.workspace }}/tmp/artifacts`. Any `${{ ... }}` expression inside a `run:` block is subject to YAML template substitution before the shell processes it, making it a script-injection risk.

Locations:

- `.github/workflows/test.yml:8`
- `.github/workflows/extraHeaders.yml:11`

### missing-permissions (severity: medium)

None of the workflow files define a top-level `permissions:` key, and no individual job within any workflow defines its own `permissions:` block. Without explicit permissions, workflows run with the repository's default token permissions, which may be overly broad (e.g., `write` access to contents and pull requests). All five workflow files are affected.

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

Fixed all three findings across five workflow files:

1. **unpinned-uses**: Pinned all `@master` action references to full commit SHAs:
   - `actions/checkout` → SHA `61b9e3751b92087fd0b06925ba6dd6314e06f089`
   - `actions/upload-artifact` → SHA `0c366cb4fc8897159c94880f94b55bc716ad6a66`
   - `amondnet/vercel-action` → SHA `9471a567336164bdfd4c8f070039943d597f6f5c`
   All retain `# master` comments for readability.

2. **script-injection**: In `test.yml` and `extraHeaders.yml`, moved `${{ github.workspace }}` out of `run:` blocks into `env:` blocks as `WORKSPACE`, then referenced `"$WORKSPACE/tmp/artifacts"` in the shell command.

3. **missing-permissions**: Added `permissions: {}` at the top level of all five workflow files (test.yml, comment-on-push.yml, extraHeaders.yml, foo-api.yml, overrides.yml) to enforce least-privilege by default.

