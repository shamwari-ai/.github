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

## Reusable workflows — there aren't any yet

A reusable workflow is a `.yml` file under `.github/workflows/` with
`on: workflow_call`, called from another repo with
`uses: shamwari-ai/.github/.github/workflows/<name>.yml@main`. As of this
writing, **this repo contains a single file: this one.** There is no
`.github/workflows/` directory, no `CODEOWNERS`, no issue or PR templates,
and no `SECURITY.md`.

Practically, that means:

- No repo in the org can currently `uses:` a shared workflow — there isn't
  one to call.
- A brand-new repo in the org inherits **nothing** automatically. It starts
  with zero CI, zero PR template, zero required checks, until someone
  copies them in by hand.
- Everything described below as "CI" is `shamwari-ai/shamwari`'s own,
  repo-local `.github/workflows/*.yml`. It is the closest thing the org has
  to a reference implementation, but it is not shared or enforced outside
  that one repo.

This is the largest concrete gap — see [Known gaps](#known-gaps). It is also
not a green field: a mature, ready-to-adopt template already exists
elsewhere in the ecosystem — see the next section.

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
exactly the kind of org-wide governance template this repo currently lacks:

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

- **Conventional Commit PR titles** are enforced by the check above on
  every PR against `shamwari-ai/shamwari`.
- **CI must pass** on `main` and `claude/**` — see the trigger rationale
  above if you're adding a workflow to a new repo.
- **An automated review gate — "Claude Approvals" — is used on PRs in this
  org.** This wasn't found codified as a workflow file, `CODEOWNERS` rule,
  or branch-protection setting readable with this pass's credentials in any
  of the three repos inspected (a direct branch-protection API read
  returned `403`, not "not configured" — this pass simply couldn't see it,
  so treat "not found in code" as "not yet inspectable," not "doesn't
  exist"). It behaves like a required status check backed by a
  bot/GitHub App rather than an in-repo workflow. Until someone with admin
  access confirms and documents its exact configuration, don't assume this
  page's silence on it means it isn't required — ask before relying on its
  absence.
- **No `CODEOWNERS` file exists** in any of the three repos, so review
  routing is manual today — there's no automatic reviewer assignment by
  path.
- **No PR or issue template exists** in any of the three repos. Nothing in
  `.github/PULL_REQUEST_TEMPLATE.md`, `.github/ISSUE_TEMPLATE/`, or this
  repo (the org default location).

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

These are real gaps, not proposals dressed up as documentation. Don't build
against any of these as if they exist:

1. **No reusable workflows adopted into this repo yet.** Every repo that
   wants CI still writes it from scratch. A mature, ready-to-adopt set
   already exists at `nyuchi/.github` (see
   [Relationship to Nyuchi Africa's org-wide defaults](#relationship-to-nyuchi-africas-org-wide-defaults)
   above) — the fix is copying/calling those, not lifting jobs out of
   `shamwari-ai/shamwari`'s own `ci.yml` into a new workflow from scratch.
   `core/`'s uv-workspace-vs-pip-compile mismatch and the org-wide
   npm-vs-pnpm mismatch blocking every JS reusable (both above) need
   resolving either way, and this page shouldn't be read as implying any
   of it has happened yet.
2. **No org-level defaults adopted** — no `CODEOWNERS`, no PR/issue
   templates, no `SECURITY.md`, in this repo or any of the three repos.
   `nyuchi/.github` already publishes `CODEOWNERS.example`,
   `dependabot.example.yml`, and real templates/`SECURITY.md` to copy and
   adapt (see above) — `CODEOWNERS` ownership itself is still a human
   decision this page can't make. A new repo in `shamwari-ai` gets none of
   this unless someone adds it by hand, every time.
3. **The "Claude Approvals" review gate is not represented as code
   anywhere this pass could inspect.** If it's a required status check, it
   should be possible to see it in branch protection or a ruleset; this
   pass's token got a `403` reading branch protection directly. Someone
   with admin access should confirm its configuration and either document
   it here or (better) codify it so it's reviewable and reproducible for a
   new repo.
4. **`shamwari-ai/docs` ships no CI of its own.** It's still the unmodified
   Mintlify starter kit at the workflow level — nothing there checks
   broken links, build failures, or the language-discipline rule that
   `shamwari`'s `docs-site`/`site` enforce, even though that repo is
   presumably subject to the same discipline.
5. **Branch protection / rulesets for these repos aren't inspectable with
   the credentials available to this pass** (org-level endpoints and the
   branch-protection endpoint both came back inaccessible). Anything above
   framed as "enforced" is enforced in the sense that CI runs and would
   fail the check — whether it's also configured as a *required* check
   that blocks merge could not be confirmed independently here.
