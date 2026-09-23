---
name: Multi-Repo Worker
description: Analyze a target repository'"'"'s dependencies and open a customized PR or issue.
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
  github-token: ${{ secrets.GH_AW_CROSS_REPO_PAT }}
  current: true
permissions:
  contents: read
tools:
  github:
    toolsets: [default]
  bash: true
safe-outputs:
  github-token: ${{ secrets.GH_AW_CROSS_REPO_PAT }}
  create-pull-request:
    target-repo: ${{ github.event.inputs.target_repo }}
    title-prefix: "[dependency-review] "
    max: 1
    draft: true
  create-issue:
    target-repo: ${{ github.event.inputs.target_repo }}
    title-prefix: "[dependency-review] "
    max: 1
---

# Dependency Review Worker

**Target repository**: ${{ github.event.inputs.target_repo }}

## Task

1. Inspect `package.json` in this repository (already checked out).
2. List all dependencies and their pinned versions.
3. Identify any dependencies that look clearly outdated based on the version numbers
   you can see (e.g., versions more than 1-2 major versions behind what you know to
   be current for common packages like lodash, express, axios, moment, react).
4. If you find at least one dependency worth flagging, create a draft pull request
   with a short markdown file named `DEPENDENCY_REVIEW.md` summarizing:
   - Each dependency and its current pinned version
   - Which ones look outdated and why
   - A suggested next step (e.g., "bump to latest minor/patch", "check for breaking
     changes before major bump")
5. If `package.json` does not exist or has no dependencies, create an issue instead
   explaining there was nothing to review.

Do not attempt to actually run `npm install` or resolve exact latest versions from the
registry; base your assessment on general knowledge of common package version
history. Be explicit that this is a lightweight heuristic review, not a full audit.