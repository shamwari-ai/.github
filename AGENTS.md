# AGENTS.md

Rules for every AI coding agent (Claude Code, Cursor, Copilot and the rest)
working in repos under [`shamwari-ai`](https://github.com/shamwari-ai).

Org `.github` repos keep agent rules in `AGENTS.md` only: no `CLAUDE.md` and
no `.claude/` directory. The enterprise-wide rules are in
[`nyuchi/.github/AGENTS.md`](https://github.com/nyuchi/.github/blob/main/AGENTS.md)
and apply here in full. This file adds what is specific to `shamwari-ai`. A repo's
own `AGENTS.md` wins over both where they conflict.

## Mission (owner, 2026-10-24)

Read this first. It applies to every repository in the Bundu ecosystem, and
every decision is judged against the whole platform, not only the repo in
front of you.

**What we are building.** A platform built to compete with the top five
platforms in the world. It is built from Africa, but not as "a product for
Africa". By default it is open data on published open standards, with no
proprietary data structures, so any system can connect to our data,
transformations and services.

**The ecosystem.** Read nyuchi.com, bundu.org, mukoko.com and mzizi.dev, and
their `llms.txt` files.

- **Bundu Foundation** governs the standards and doctrine. Ubuntu is the spec:
  survival, solidarity, compassion, respect, dignity. Personal data stays on
  the user's device or pod, under the user's keys. No surveillance business
  model. The data commons is open.
- **Nyuchi Africa** is the frontier infrastructure (API Platform, Pay, Web
  Services, Weather Stations, Logistics and more), with the console at
  platform.nyuchi.com.
- **Mukoko** is a privacy-first consumer super-app: one identity, one payments
  rail, offline-first sync, and English, Shona and Ndebele at parity. Kweli is
  its trust and verification layer.
- **Mzizi** is the open architecture framework and language, built on Rust.
  Mzizi is the rule: no app drifts from it, and filling a design-system gap
  so an app conforms to Mzizi needs no approval.
- **Four gateways** (nyuchi-api#353), each rebuilt in Rust on the same layout,
  core, origin and edge (nyuchi-api#355):
  - Mzizi API: public.
  - Nyuchi API: B2B, api.nyuchi.com.
  - Mukoko API: consumer.
  - Nyuchi Servers API: internal, no public IP.

**The engineering bar**, for every service and data model:

1. **Open standards by default** (nyuchi-api#369). Never invent a format when
   a published standard exists:
   - schema.org types with JSON-LD; GeoJSON (RFC 7946) for places;
   - ISO 3166 for countries, ISO 4217 for currencies, RFC 3339 for times,
     BCP 47 for languages (`en`, `sn`, `nd`);
   - CloudEvents for events and DCAT for open datasets;
   - OpenAPI 3.1 for every API;
   - RFC 9457 Problem Details for every error;
   - OAuth 2.1 and OIDC, with RFC 9728 protected-resource metadata;
   - W3C Trace Context and OpenTelemetry;
   - IETF RateLimit headers.
2. **Nothing crashes.** A handler panic becomes a 500 and the process keeps
   serving. Requests have timeouts and size limits. Services shut down
   gracefully. `/health` is liveness and `/ready` checks each dependency. The
   version reported is the real release, never hard-coded.
3. **Everything ships to production.** Releases go `staging` → `main` through
   the merge queue. Deploys run on every push to `main`, with no variable
   gates. A missing secret fails loudly, never silently skips.
4. **Never close a conflicting PR.** Resolve the conflict and keep every
   feature.
5. **Think as a data scientist, a software engineer, a psychologist and an
   engagement designer at once.** Every record is linkable across systems by
   one canonical id: WorkOS `externalId` = Mongo entity id = ledger
   `owner_id`. An empty screen loses a user, so design for engagement and
   trust.
6. **Honesty.** Report what is measured, label design as design, and never
   overclaim.

**Dependencies.** Declare first-party packages (`@nyuchi/*`, `@bundu/*`,
`mzizi-*`) and direct dependencies as minimum versions (`>=x.y.z`) with no
upper bound; an old version blocks, the latest never does. Third-party
selectors known to break across majors, and runtime dependencies of
published packages, are capped within their major (`>=x <next-major`).

## Branches, versions and merging

- Branch off `staging` and open pull requests into `staging`, as drafts.
  Agent branches start with `claude/` (or the agent's own prefix).
- Each merge into `staging` is tagged as the next patch and each release to
  `main` as the next minor (`staging-version.yml`, using `nyuchi/.github`'s
  versioning policy). Never hand-edit a version to a major.
- PR titles are Conventional Commits: lowercase, imperative subject, no
  trailing period.
- The org squash-merges, so the PR title becomes the commit subject on `main`. Keep history linear: rebase, never merge `main` into your branch.
- One writer per branch. Land with `gh pr merge <n> --auto`, which joins the
  merge queue where a branch requires one. Treat GitHub 5xx errors and "merge
  already in progress" as a wait, not a failure.

## Checks that gate every pull request

- `org-lint.yml` in this repo is required on every PR in the org.
- The enterprise rulesets also run `nyuchi/.github`'s release version check,
  dependency review and Semgrep on every PR into `staging` and `main`.
- Every workflow whose check is required must also trigger on `merge_group:`.
- Never disable or weaken a check to get green; fix the cause or escalate.
