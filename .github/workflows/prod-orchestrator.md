---
name: Production Dependency Orchestrator
description: Dispatch dependency remediation worker across a fixed set of production target repositories.
on:
  workflow_dispatch:
permissions:
  contents: read
  copilot-requests: write
tools:
  github:
    toolsets: [default]
model: claude-sonnet-5
safe-outputs:
  dispatch-workflow:
    workflows: [prod-worker]
    max: 5
---

# Production Dependency Orchestrator

You are orchestrating a dependency review across a fixed list of production
target repositories. This is a controlled, low-volume test run.

## Target repositories

- elanco/commercial-web-portfolioPet
- elanco/Manufacturing-KPIT

## Task

1. For each target repository listed above, dispatch the `prod-worker` workflow,
   passing the repository name as the `target_repo` input.
2. Summarize which repositories were dispatched.

Do not analyze the repositories yourself here; that is the worker's job.
