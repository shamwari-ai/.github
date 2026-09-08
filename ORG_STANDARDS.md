# Org CI & Governance Standards

This page describes the **current, actual** state of governance across the
`shamwari-ai` org, as of this writing. Where something is a proposal rather
than something running today, it's called out explicitly under
[Known gaps](#known-gaps) — nothing here should be read as already codified
unless it says so.

## Repos this covers

- **[shamwari-ai/.github](https://github.com/shamwari-ai/.github)** — this
  repo. The org's default community-health repo. GitHub falls back to
  whatever lives here for any repo that doesn't define its own issue/PR
  templates, `CODEOWNERS`, or `SECURITY.md`.
- **[shamwari-ai/shamwari](https://github.com/shamwari-ai/shamwari)** — the
  product monorepo (`gateway/`, `core/`, `docs-site/`, `site/`). Its
  `CLAUDE.md` is the authoritative source for *why* several of these checks
  exist; this page doesn't restate that reasoning, it maps it onto the CI
  that runs.
- **[shamwari-ai/docs](https://github.com/shamwari-ai/docs)** — the public
  product documentation site (docs.shamwari.ai). Org/governance standards
  don't belong there — that's what this file is for.
- **The extraction repos**, created 2026-09-08 against the plan in
  `shamwari`'s `docs/repo-split.md`:
  [shamwari-gateway](https://github.com/shamwari-ai/shamwari-gateway),
  [shamwari-web](https://github.com/shamwari-ai/shamwari-web),
  [shamwari-platform](https://github.com/shamwari-ai/shamwari-platform),
  [shamwari-core](https://github.com/shamwari-ai/shamwari-core), plus the
  parked [shamwari-mind](https://github.com/shamwari-ai/shamwari-mind) and
  [shamwari-sandbox](https://github.com/shamwari-ai/shamwari-sandbox).
  All six are public. Only `shamwari-platform` has content; the other five
  are deliberately empty so `git subtree split` can push history in without
  a merge or a force.

## Reusable workflows

A reusable workflow is a `.yml` file under `.github/workflows/` with
`on: workflow_call`, called from another repo with
`uses: shamwari-ai/.github/.github/workflows/<name>.yml@main`.

As of **2026-09-08** this repo publishes six:

| Workflow | For | Notes |
|---|---|---|
| `reusable-ci-node-npm.yml` | `shamwari-gateway` | typecheck / test / build jobs, each skippable by passing an empty script name |
| `reusable-ci-astro-npm.yml` | `shamwari-web`, `shamwari-platform` | `npm run build` then `npm run check` — the repo-local `check.mjs`, kept as its own required step |
| `reusable-ci-python-piptools.yml` | `shamwari-core` | `check_lock.py`, then `pip install --require-hashes`, then an import check |
| `reusable-pr-title-lint.yml` | every repo | Conventional Commits on the PR title; third-party action pinned by SHA |
| `reusable-gitleaks.yml` | every repo | runs the MIT-licensed binary directly, not the paid-licence wrapper action |
| `reusable-codeql.yml` | every repo with code | static analysis; `shamwari`'s local `ci.yml` still doesn't run this |

These are **npm-native and pip-tools-native forks**, not calls into
`nyuchi/.github`. That is a deliberate choice, and the reason is in the next
section: every JS reusable upstream hardcodes pnpm, and the Python one
assumes a uv workspace. Forking also means this org pins its own action
SHAs rather than inheriting whatever `@main` points at in another org.

`required_status_checks` is deliberately **not** yet part of the ruleset —
see [Branch and PR requirements](#branch-and-pr-requirements).

## Relationship to Nyuchi Africa's org-wide defaults

**Bundu Foundation governs four pillars, and Nyuchi and Shamwari AI are two
of them — siblings, not parent and child.** Per [bundu.org](https://bundu.org):
"Four pillars: Bundu Labs (research), Mukoko (consumer), Nyuchi (commercial),
Shamwari AI (community)." Nyuchi Africa is the commercial pillar; Shamwari AI
is the community pillar. Shamwari's IP is owned by Bundu Foundation and, per
bundu.org, is "implemented across Nyuchi and Mukoko surfaces and sold
commercially under Nyuchi" — Nyuchi is Shamwari's commercial *operator*, not
its corporate parent. This matches `shamwari-ai/shamwari`'s own `CLAUDE.md`
exactly: "IP owner: Bundu Foundation (Zimbabwe CLG) · Sold commercially
under: Nyuchi Africa." (That same `CLAUDE.md` flags a known upstream
inconsistency worth being precise about here: a separate Mzizi-registry
source reportedly describes three pillars with Nyuchi as *parent* — that
version contradicts bundu.org and should not be treated as authoritative.)

That sibling relationship — not a parent/subsidiary one — is *why* looking
at Nyuchi's own governance is useful here: both pillars share "the same
identity layer, the same design system, the same engineering doctrine"
(bundu.org) under the same Foundation, and Nyuchi's commercial role in
operating Shamwari gives it practical reason to keep its CI/governance
tooling reusable. Nyuchi Africa runs a separate GitHub org —
[`nyuchi`](https://github.com/nyuchi) — whose
[`nyuchi/.github`](https://github.com/nyuchi/.github) repo is public and is
exactly the kind of org-wide governance template this repo was modelled on:

- **Reusable workflows** under `.github/workflows/reusable-*.yml`, including
  `reusable-ci-typescript.yml`, `reusable-ci-typescript-lib.yml`,
  `reusable-ci-python-monorepo.yml`, `reusable-ci-docs-mdx.yml`,
  `reusable-pr-title-lint.yml`, `reusable-codeql.yml`,
  `reusable-dependency-review.yml`, `reusable-sbom.yml`,
  `reusable-slsa-provenance.yml`, `reusable-openssf-scorecard.yml`, and
  `reusable-stale.yml` — several of which (CodeQL, dependency review, SBOM,
  SLSA provenance, Scorecard) `shamwari-ai/shamwari`'s local `ci.yml`
  doesn't run at all today.
- **Starter templates meant to be copied and adapted**: `CODEOWNERS.example`
  and `.github/dependabot.example.yml`.
- **Real, populated community-health files**:
  `.github/PULL_REQUEST_TEMPLATE.md`, `.github/ISSUE_TEMPLATE/*.yml`,
  `SECURITY.md`, `CODE_OF_CONDUCT.md`, `CONTRIBUTING.md`, `SUPPORT.md`,
  `CLA.md`.
- **`ORG_SETTINGS.md`** plus actual `github-rulesets/*.json` ruleset
  definitions — branch protection as reviewable, versioned JSON rather than
  only dashboard configuration (relevant to gaps 3 and 5 below).
- **`profile/governance/NA-01/02/03`** — a corporate constitution, an
  open-source governance policy, and an engineering working agreement.
  `NA-03_ENGINEERING.md` explicitly scopes itself to "Platform and product
  work (Mukoko, Nyuchi Enterprise, sister brands...)" — "sister brands" is
  the accurate framing: Shamwari is a sibling Bundu pillar Nyuchi operates
  commercially, not a Nyuchi-owned product line.

**None of this reaches `shamwari-ai` automatically.** `nyuchi` and
`shamwari-ai` are separate GitHub orgs. The fallback mechanism that makes
this repo apply to any repo *inside* `shamwari-ai` is scoped to that one
org — it has no cross-org equivalent. Nothing in `nyuchi/.github` currently
propagates into `shamwari-ai` on its own; every adoption is a deliberate
copy (for templates, `CODEOWNERS`, `SECURITY.md`) or an explicit
`uses: nyuchi/.github/.github/workflows/<name>.yml@main` reference inside a
workflow file (for the reusable workflows — permitted across orgs since the
repo is public).

**The recommended path is adoption, not reinvention** — but two fit
problems block a straight swap, flagged rather than papered over:

- **Python**: `reusable-ci-python-monorepo.yml` assumes a **uv workspace**
  (root `pyproject.toml` with `[tool.uv.workspace]`, a committed `uv.lock`,
  packages under `packages/<name>/`). `core/`'s actual dependency setup is
  `requirements.in` → `pip-compile --generate-hashes` → `requirements.txt`,
  a different shape entirely — this reusable workflow is **not** a drop-in
  replacement for `core`'s CI job without either migrating `core/` to a uv
  workspace or adapting the workflow (or writing a new one) to the
  lock-and-hash pattern `CLAUDE.md` requires.
- **JavaScript/TypeScript, org-wide**: `gateway/`, `docs-site/`, and
  `site/` all use **npm** (`package-lock.json`, no `.nvmrc`; `gateway/` has
  no `lint` script at all). `reusable-ci-typescript.yml`,
  `reusable-ci-typescript-lib.yml`, and `reusable-ci-docs-mdx.yml` all
  hardcode `pnpm/action-setup` + `pnpm install --frozen-lockfile` — there is
  no npm code path in any of them. None of the three JS reusables are a
  drop-in for any of this repo's JS packages without first migrating the
  whole JS stack from npm to pnpm (or forking npm-compatible variants).
  Separately, `reusable-ci-docs-mdx.yml` runs cspell + lychee + a generic
  build command — it has **no equivalent** of `check.mjs`'s dead-asset
  checks, `:root`-token color checks, size budget, or the "open weights"
  language-discipline rule (see `CLAUDE.md`). Adopting it as-is would trade
  one set of checks for a different, non-overlapping set, not a strict
  upgrade — `check.mjs` would need to keep running alongside it, or get
  folded into a forked variant.

So "adoption" for the JS side is gated on a repo-wide npm→pnpm migration
decision, not a config tweak — a bigger blocker than it might first look.
None of this is implemented in this pass — see the tracked issues linked
from Known gaps below.

## CI that actually runs, today (`shamwari-ai/shamwari`)

Two workflow files, both repo-local:

- **`ci.yml`** — Five jobs: `gateway`, `core`, `docs-site`, `site`,
  `gitleaks`. Runs on push to `main` and on pull requests targeting `main`
  **or** `claude/**`.
- **`pr-title-lint.yml`** — One job enforcing Conventional Commits on the PR
  title, via `amannn/action-semantic-pull-request`.

### Why PRs are checked against `claude/**`, not just `main`

```yaml
on:
  push:
    branches: [main]
  pull_request:
    branches: [main, "claude/**"]
```

Stacked PRs target the branch below them in the stack, not `main`. A filter
of just `[main]` would silently give every layer but the bottom one zero
CI — a green PR with no checks run, which looks identical to a passing one.
Keep this in mind if you're setting up CI for a new repo: filtering PR
triggers to `main` alone is the mistake this pattern exists to avoid.

### The `ci.yml` jobs

| Job | What it does | Why |
|---|---|---|
| **gateway** | `npm run typecheck` and `npm test` in `gateway/` | Per `CLAUDE.md`, gateway unit tests are what cover the two rules that must not be broken: personal-scope requests never resolve to a cloud destination, and the premium tier stays `licenseClass: restricted`. |
| **core** | `python check_lock.py`, then `pip install --require-hashes -r requirements.txt`, then an import check | `core/requirements.txt` is generated by `pip-compile --generate-hashes` from the hand-edited `requirements.in` and must never be hand-edited. `check_lock.py` fails if the spec names something the lock doesn't pin, offline and before install, so a stale lock is named as such instead of surfacing as a confusing `ImportError`. `--require-hashes` is there so a dependency line *without* a hash fails the install, rather than quietly weakening the guarantee for the whole file. |
| **docs-site** | `npm run build`, then `npm run check` | `astro check` covers types/MDX; the repo's own `check.mjs` covers what it can't — dead links/assets, colors with no base `:root` token, stray client JS, a size budget, and the "open weights" language-discipline wording rule. |
| **site** | Same shape as `docs-site`, for the marketing site | Same rationale, applied to `site/`. |
| **gitleaks** | Downloads the `gitleaks` binary directly and runs `gitleaks detect --source . --redact --no-banner --verbose` | The `gitleaks/gitleaks-action` wrapper now requires a paid license for org repos; the underlying binary is still MIT-licensed, so the workflow runs it directly to keep the scan free. |

`ci.yml` sets one `concurrency` group for the whole workflow —
`${{ github.workflow }}-${{ github.ref }}`, `cancel-in-progress: true` — so
a new push to the same PR or branch cancels the `ci.yml` run in flight
rather than queuing behind it. `pr-title-lint.yml` has its own, separate
concurrency group (see below); the two workflows don't share one.

### `pr-title-lint.yml`

Enforces Conventional Commits on the PR title (not commit messages) via
`amannn/action-semantic-pull-request`. Its own concurrency group is
`${{ github.workflow }}-${{ github.event.pull_request.number }}` — keyed by
PR number, not ref, since it only ever runs on `pull_request` events.

- Allowed types: `feat`, `fix`, `perf`, `refactor`, `docs`, `test`, `build`,
  `ci`, `chore`, `revert`, `style`.
- No required scope.
- Subject must start lowercase, read as an imperative, and not end in a
  period.
- `wip: true` — exempts titles that literally *start with* `wip` (case
  insensitive), e.g. `wip: still figuring out the schema`. This is **not**
  the same thing as GitHub's draft-PR flag: a draft PR with an ordinary
  title still gets checked and can still fail. (PR #24 in
  `shamwari-ai/shamwari` is a live example — it was a draft with a
  non-`wip` title and the check failed until the title was fixed.)

## Branch and PR requirements

Since **2026-09-08** these are enforced by an org-level ruleset, defined as
versioned JSON in [`github-rulesets/`](./github-rulesets/) and applied to
the org. Previous versions of this page said branch protection "wasn't
inspectable" — that was a token-scope problem, not a configuration one. Read
with an `admin:org` token, the honest answer at the time was **nothing was
configured at all**: no org rulesets, no repo rulesets, no branch protection
on any repo.

`org-wide-main-protection` applies to every repo's default branch:

| Rule | Effect |
|---|---|
| `deletion` | The default branch cannot be deleted |
| `non_fast_forward` | No force-pushes |
| `required_linear_history` | Rebase, don't merge `main` into your branch |
| `required_signatures` | Every commit must be signed |
| `pull_request` | Changes land via PR, squash-merge only, review threads resolved |

`release-tag-protection` makes `v*` tags immutable once pushed.

Two choices worth understanding before you trip over them:

- **`required_approving_review_count` is 0.** The org has one member. A
  count of 1 would make every PR unmergeable by its own author — a lockout,
  not a safeguard. Raise it to 1 the day a second maintainer joins.
- **Organisation admins can bypass.** The repo split imports history with
  `git subtree split` pushed straight to `main`, which the `pull_request`
  rule would otherwise reject. Narrow or remove that bypass once the
  extractions are done.

**`required_status_checks` is deliberately absent.** Naming a check context
that has never reported makes every PR permanently unmergeable. Add contexts
only after CI has run at least once on that repo and you can read the exact
job names off a completed run.

- **Conventional Commit PR titles** are enforced by
  `reusable-pr-title-lint.yml`. Because the org squash-merges, the PR title
  becomes the commit subject on `main`.
- **CI triggers must cover `main` and `claude/**`** — see the trigger
  rationale above if you're adding a workflow to a new repo.
- **`CODEOWNERS` now exists** at `.github/CODEOWNERS` in this repo, so it
  applies org-wide by fallback. It names `@bryanfawcett` as the catch-all
  owner and calls out the rule 1 paths explicitly. `require_code_owner_review`
  is off in the ruleset while the org has one member.
- **The "Claude Approvals" review gate is still not represented as code.**
  This pass could read branch protection and rulesets successfully and found
  none, so whatever that gate is, it is **not** a required status check or a
  ruleset — it behaves like a GitHub App acting on PRs. Its configuration
  still lives outside this repo.
- **PR and issue templates now exist** in this repo, at
  `.github/PULL_REQUEST_TEMPLATE.md` and `.github/ISSUE_TEMPLATE/`, and
  apply org-wide by fallback.

## Best practices actually enforced (not aspirational)

Pulled from what the workflow files actually do, not from what would be
nice:

- **Pinned, hashed dependencies for anything that enforces the scope
  gate.** `core/`'s CI fails before install if the lockfile doesn't match
  the spec, and fails install if any pinned line is missing a hash. This
  exists because `core/main.py::resolve_scope` is the authoritative half of
  rule 1 (personal-scope data must never reach a third-party inference
  provider) — see `shamwari-ai/shamwari`'s `CLAUDE.md`.
- **Secret scanning on every push and PR**, running the raw `gitleaks`
  binary rather than a wrapper action, specifically to avoid a licensing
  dependency.
- **Docs and marketing sites are build-checked, not just deployed.**
  `docs-site` and `site` both run `astro check` plus a custom `check.mjs`
  that enforces link/asset integrity, a size budget, and the org's own
  language-discipline rule (e.g. "open weights," never "open source
  model" — see the Language discipline table in `shamwari-ai/shamwari`'s
  `CLAUDE.md`).
- **Every workflow cancels superseded runs of itself**, each via its own
  `concurrency` group (see above) — a new push doesn't leave a stale run of
  that workflow occupying a runner. The two workflows in this repo don't
  share a group with each other.

## Known gaps

Updated 2026-09-08. Gaps 1-3 and 5 below were closed in this pass; what
remains is listed as remaining.

**Closed:**

1. ~~No reusable workflows.~~ Six now published — see
   [Reusable workflows](#reusable-workflows). They are npm/pip-tools forks
   rather than `nyuchi/.github` calls, because the pnpm and uv mismatches
   below are real and unresolved.
2. ~~No org-level defaults.~~ `CODEOWNERS`, `PULL_REQUEST_TEMPLATE.md`,
   `ISSUE_TEMPLATE/`, `SECURITY.md`, `CONTRIBUTING.md`,
   `CODE_OF_CONDUCT.md`, `SUPPORT.md` and `dependabot.example.yml` now
   exist here and apply org-wide by fallback.
3. ~~Branch protection not inspectable.~~ It was a token-scope problem. With
   `admin:org`, the answer was "nothing configured"; an org ruleset now
   exists and is versioned in `github-rulesets/`.

**Remaining:**

4. **The npm-vs-pnpm and pip-compile-vs-uv mismatches still stand.** This
   pass worked *around* them by forking npm-native and pip-tools-native
   workflows rather than resolving them. If the org later wants to adopt
   `nyuchi/.github`'s workflows directly, the JS stack still needs an
   npm→pnpm migration and `core/` still needs a uv workspace. Neither is a
   config tweak.
5. **No `required_status_checks` in the ruleset yet.** Deliberate — a check
   context that has never reported blocks every PR. Add them per repo once
   CI has run once.
6. **The "Claude Approvals" gate remains uncodified.** Now known *not* to be
   a ruleset or branch protection, since both were read successfully and are
   accounted for. Someone should identify the App behind it and document it.
7. **`shamwari-ai/docs` still ships no CI.** A branch exists that adds it —
   `claude/docs-ci`, one commit, unmerged — so this is unfinished work
   rather than untouched ground.
8. **Nothing is deployed yet.** `shamwari-gateway` and `shamwari-core` are
   written and not deployed; `shamwari.knowledgeBase` is still empty. The
   repo-split plan's own advice — "do not split before the demo works" —
   still applies: these repos exist, but moving code into them competes with
   the task that turns this into a working product.
9. **Five of the six new repos are empty.** Only `shamwari-platform` has
   content. The extractions themselves have not been done.
