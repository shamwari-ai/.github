# .github

The `shamwari-ai` org's default community-health repo. GitHub falls back to
whatever lives here — `CODEOWNERS`, issue and PR templates, `SECURITY.md` —
for any repo in the org that doesn't define its own. It's also where the
org's reusable GitHub Actions workflows (`on: workflow_call`) live.

## What's here

| Path                                                                 | Applies to                                             |
| -------------------------------------------------------------------- | ------------------------------------------------------ |
| `.github/CODEOWNERS`                                                 | Every repo without its own                             |
| `.github/PULL_REQUEST_TEMPLATE.md`                                   | Every repo without its own                             |
| `.github/ISSUE_TEMPLATE/`                                            | Every repo without its own                             |
| `.github/workflows/reusable-*.yml`                                   | Called explicitly with `uses:`                         |
| `.github/dependabot.example.yml`                                     | Copy per repo — Dependabot config does **not** inherit |
| `github-rulesets/*.json`                                             | The applied org ruleset, as reviewable JSON            |
| `SECURITY.md`, `CONTRIBUTING.md`, `CODE_OF_CONDUCT.md`, `SUPPORT.md` | Every repo without its own                             |

## Using a reusable workflow

```yaml
# .github/workflows/ci.yml in a consuming repo
name: CI
on:
  push:
    branches: [main]
  pull_request:
    branches: [main, "claude/**"]   # not just main — see ORG_STANDARDS.md

concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true

jobs:
  ci:
    uses: shamwari-ai/.github/.github/workflows/reusable-ci-node-npm.yml@main
  secrets:
    uses: shamwari-ai/.github/.github/workflows/reusable-gitleaks.yml@main
```

Available: `reusable-ci-node-npm`, `reusable-ci-astro-npm`,
`reusable-ci-python-piptools`, `reusable-pr-title-lint`,
`reusable-gitleaks`, `reusable-codeql`.

## Read this first

[**ORG_STANDARDS.md**](./ORG_STANDARDS.md) — what CI actually runs across
the org, what the ruleset enforces, and the gaps that are still open. It
distinguishes throughout between what is running and what is merely
proposed; nothing there should be read as codified unless it says so.
