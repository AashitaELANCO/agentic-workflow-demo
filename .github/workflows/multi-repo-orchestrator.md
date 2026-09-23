---
name: Multi-Repo Dependabot Orchestrator
description: Dispatch dependency remediation worker across a fixed set of target repositories.
on:
  workflow_dispatch:
permissions:
  contents: read
tools:
  github:
    toolsets: [default]
model: claude-sonnet-5
safe-outputs:
  dispatch-workflow:
    workflows: [multi-repo-worker]
    max: 5
---

# Multi-Repo Orchestrator

You are orchestrating a dependency review rollout across a fixed list of target
repositories.

## Target repositories

- AashitaELANCO/target-repo-a
- AashitaELANCO/target-repo-b

## Task

1. For each target repository listed above, dispatch the `multi-repo-worker` workflow,
   passing the repository name as the `target_repo` input.
2. Summarize which repositories were dispatched.

Do not analyze the repositories yourself here; that is the worker'"'"'s job.
