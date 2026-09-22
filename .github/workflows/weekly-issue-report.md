---
on:
  workflow_dispatch:
  schedule:
    - cron: "0 9 * * 1"

permissions:
  issues: read
  contents: read
  copilot-requests: write

network: defaults

tools:
  github:
    toolsets: [issues]

safe-outputs:
  create-issue:

engine: copilot
---

# Weekly issue activity report

Review issue activity from the last 7 days in this repository.

Create a GitHub issue that includes:

- Total issues opened and closed this week.
- The top recurring themes from issue titles and descriptions.
- A short list of notable issues that still need attention.
- Two or three actionable recommendations for maintainers.

Keep the report concise and action-oriented.