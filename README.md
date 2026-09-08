# .github

This is the `shamwari-ai` org's default community-health repo: GitHub falls back to whatever lives here (`CODEOWNERS`, issue/PR templates, `SECURITY.md`) for any repo in the org that doesn't define its own. It's also where org-wide reusable GitHub Actions workflows (`on: workflow_call`) would live, for any repo to `uses:`.

None of that exists here yet. For the current, actual state of CI and governance across the org - what this repo provides today (almost nothing), what `shamwari-ai/shamwari`'s CI actually runs, and where the gaps are - see [ORG_STANDARDS.md](./ORG_STANDARDS.md) in this repo. It also tracks the concrete gaps (reusable workflows, CODEOWNERS, the "Claude Approvals" review gate, etc.) as tracked issues.
