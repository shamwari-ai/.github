# Contributing to Shamwari AI

## Before you start

Read `CLAUDE.md` in [`shamwari-ai/shamwari`](https://github.com/shamwari-ai/shamwari).
It is the authoritative record of _why_ several of the checks below exist,
and it carries the applied-migration log for the live databases. This page
does not restate that reasoning.

## The two rules

Everything else here is a preference. These two are not:

1. **`personal`-scope data must never reach a third-party inference
   provider.** The gate is implemented twice on purpose —
   `gateway/src/scope.ts` fails fast, `core/main.py::resolve_scope` is
   authoritative. **Do not deduplicate them into a shared package.** Two
   independent implementations is the design; collapsing them into one
   leaves the sovereignty claim resting on a single function.
2. **The premium tier stays `licenseClass: restricted`.**

A PR that touches routing, scopes, or `licenseClass` gets read against both.

## Pull requests

- **Titles follow Conventional Commits.** `feat:`, `fix:`, `perf:`,
  `refactor:`, `docs:`, `test:`, `build:`, `ci:`, `chore:`, `revert:`,
  `style:`. Subject lowercase, imperative, no trailing period. A title
  starting with `wip` is exempt — that is not the same as marking the PR a
  draft, which does not exempt it.
- **The title becomes the commit subject.** The org squash-merges, so a
  vague title becomes a vague line in `main`'s history permanently.
- **Stacked PRs target the branch below them**, not `main`. CI triggers are
  configured for `main` and `claude/**` for exactly this reason — a filter
  of `[main]` alone gives every layer but the bottom one zero checks, and a
  PR with no checks run looks identical to a passing one.
- **Commits must be signed.** The org's ruleset requires it. SSH signing is
  the simplest route: `git config gpg.format ssh` and
  `git config user.signingkey ~/.ssh/id_ed25519.pub`.
- **Linear history.** Rebase rather than merge `main` into your branch.

## Dependencies

- **Python (`shamwari-core`)**: edit `requirements.in`, then regenerate with
  `pip-compile --generate-hashes`. **Never hand-edit `requirements.txt`.**
  CI runs `check_lock.py` offline before installing, so a stale lock is
  named as such instead of surfacing later as a confusing `ImportError`,
  and installs with `--require-hashes` so a line missing a hash fails
  rather than quietly weakening the guarantee for the whole file.
- **JavaScript/TypeScript**: npm, with a committed `package-lock.json`.
  The org is on npm, not pnpm — see
  [ORG_STANDARDS.md](./ORG_STANDARDS.md) for why that matters when reading
  workflows borrowed from `nyuchi/.github`.

## Language discipline

User-facing copy has wording rules, enforced by each site's `check.mjs` —
most notably **"open weights"**, never "open source model". The full table
is in `shamwari`'s `CLAUDE.md`. CI fails the build on a violation; this is
not a style preference.

## Running the checks locally

| Repo                                | Command                                                                    |
| ----------------------------------- | -------------------------------------------------------------------------- |
| `shamwari-gateway`                  | `npm ci && npm run typecheck && npm test`                                  |
| `shamwari-core`                     | `python check_lock.py && pip install --require-hashes -r requirements.txt` |
| `shamwari-web`, `shamwari-platform` | `npm ci && npm run build && npm run check`                                 |

## Reporting security issues

Do not open a public issue. See [SECURITY.md](./SECURITY.md).
