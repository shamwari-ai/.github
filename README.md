# Shamwari AI — org defaults

> The `shamwari-ai` org's community-health repo and its reusable GitHub Actions workflows.

[![Lint](https://github.com/shamwari-ai/.github/actions/workflows/lint.yml/badge.svg)](https://github.com/shamwari-ai/.github/actions/workflows/lint.yml)

**Read first:** [ORG_STANDARDS.md](https://github.com/shamwari-ai/.github/blob/main/ORG_STANDARDS.md) | **Org:** [shamwari-ai](https://github.com/shamwari-ai) | **Product:** [shamwari.ai](https://shamwari.ai)

---

## What it is

The org's default community-health repo. GitHub falls back to
whatever lives here — `CODEOWNERS`, issue and PR templates, `SECURITY.md` —
for any repo in the org that doesn't define its own. It's also where the
org's reusable GitHub Actions workflows (`on: workflow_call`) live.

## What's here

| Path                                                                 | Applies to                                             |
| -------------------------------------------------------------------- | ------------------------------------------------------ |
| `profile/README.md`                                                  | The org page at <https://github.com/shamwari-ai>       |
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

Available here: `reusable-ci-node-npm`, `reusable-ci-astro-npm`,
`reusable-ci-python-piptools`, `reusable-pr-title-lint`,
`reusable-gitleaks`, `reusable-codeql`.

Two things the snippet above does not show:

- **The lint gate is not in this repo.** Every `shamwari-ai` repo carries a
  `.github/workflows/lint.yml` that calls
  `nyuchi/.github/.github/workflows/reusable-lint.yml@main`, along with
  `.prettierrc`, `.prettierignore`, `.markdownlint.jsonc` and `.yamllint.yaml`
  copied into the repo root. It publishes the five contexts the org ruleset
  requires — `lint / actionlint`, `lint / JSON validity`, `lint / prettier`,
  `lint / markdownlint`, `lint / yamllint` — and those strings depend on the
  calling job being named `lint`. Do not convert it to a matrix.
- **`branches: [main]` is not right everywhere.** `shamwari-core`,
  `shamwari-gateway` and `shamwari-web` default to `scaffold` while they wait
  for their `git subtree split` imports, so a `push` filter of `[main]` gives
  them no runs on their own default branch. Match the repo's actual default
  branch, or the badge reads "no status" and nobody notices.

## Read this first

[**ORG_STANDARDS.md**](https://github.com/shamwari-ai/.github/blob/main/ORG_STANDARDS.md)
— what CI actually runs across the org, what the ruleset enforces, and the
gaps that are still open. It distinguishes throughout between what is running
and what is merely proposed; nothing there should be read as codified unless
it says so.

## Ecosystem

| Repo                                                                    |                                                                    |
| ----------------------------------------------------------------------- | ------------------------------------------------------------------ |
| [`shamwari`](https://github.com/shamwari-ai/shamwari)                   | the product monorepo — gateway, core, site, docs-site, db          |
| [`shamwari-web`](https://github.com/shamwari-ai/shamwari-web)           | [shamwari.ai](https://shamwari.ai) — scaffold, awaiting extraction |
| [`shamwari-gateway`](https://github.com/shamwari-ai/shamwari-gateway)   | the edge Worker — scaffold, awaiting extraction                    |
| [`shamwari-core`](https://github.com/shamwari-ai/shamwari-core)         | the FastAPI service — scaffold, awaiting extraction                |
| [`shamwari-platform`](https://github.com/shamwari-ai/shamwari-platform) | the customer console — not started                                 |
| [`shamwari-sandbox`](https://github.com/shamwari-ai/shamwari-sandbox)   | the Rust execution host — not started                              |
| [`shamwari-mind`](https://github.com/shamwari-ai/shamwari-mind)         | training pipeline and eval harness — not started                   |
| [`docs`](https://github.com/shamwari-ai/docs)                           | [docs.shamwari.ai](https://docs.shamwari.ai) — live                |

## Licence

This repository has no `LICENSE` file. It holds configuration and
community-health documents rather than software, and nothing here ships in a
package. The product repos are
[Apache-2.0](https://www.apache.org/licenses/LICENSE-2.0).

© Bundu Foundation. Shamwari is Bundu Foundation IP, sold commercially under
Nyuchi Africa.
