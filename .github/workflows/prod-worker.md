---
name: Production Worker
description: Analyze a target production repository's dependencies and open one customized draft PR or issue.
on:
  workflow_dispatch:
    inputs:
      target_repo:
        description: "Target repository (owner/repo format)"
        required: true
        type: string
run-name: Dependency review for ${{ github.event.inputs.target_repo }}
concurrency:
  group: gh-aw-${{ github.workflow }}-${{ github.event.inputs.target_repo }}
checkout:
  repository: ${{ github.event.inputs.target_repo }}
  github-token: ${{ secrets.GH_AW_PROD_CROSS_REPO_PAT }}
  current: true
permissions:
  contents: read
  copilot-requests: write
tools:
  github:
    toolsets: [default]
  bash: true
model: claude-sonnet-5
safe-outputs:
  github-token: ${{ secrets.GH_AW_PROD_CROSS_REPO_PAT }}
  create-pull-request:
    target-repo: ${{ github.event.inputs.target_repo }}
    allowed-repos:
      - elanco/commercial-web-portfolioPet
      - elanco/Manufacturing-KPIT
    title-prefix: "[dependency-review] "
    max: 1
    draft: true
  create-issue:
    target-repo: ${{ github.event.inputs.target_repo }}
    allowed-repos:
      - elanco/commercial-web-portfolioPet
      - elanco/Manufacturing-KPIT
    title-prefix: "[dependency-review] "
    max: 1
---

# Dependency Review Worker (Production, Controlled Test)

**Target repository**: ${{ github.event.inputs.target_repo }}

This is a controlled first test against a real production repository. Be
conservative and precise.

## Task

1. Inspect the dependency manifest(s) present in this repository (e.g.
   `package.json`, `.csproj`, or equivalent, whichever actually exist here).
2. List the dependencies you find and their pinned versions.
3. Identify at most 3-5 dependencies that look clearly outdated or carry a
   known security concern, based on general knowledge of version history.
   Do not attempt to query any registry or run installs.
4. Create exactly one draft pull request containing a single new markdown
   file named `DEPENDENCY_REVIEW.md` that summarizes:
   - Each flagged dependency and its current pinned version
   - Why it looks outdated or risky
   - A conservative suggested next step (e.g. "bump to latest patch",
     "flag for manual review before any major bump")
5. Do not modify any other file. Do not attempt to actually change dependency
   versions in this pass -- this is a reporting-only pull request.
6. If no manifest is found or nothing stands out, create an issue instead
   explaining that clearly, and do not create a pull request.

Be explicit in the PR description that this is an automated, conservative,
heuristic-based review -- not a full security audit -- and that no dependency
versions were actually changed in this pass.
