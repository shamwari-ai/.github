# AGENTS.md

Rules for every AI coding agent (Claude Code, Cursor, Copilot and the rest)
working in repos under [`shamwari-ai`](https://github.com/shamwari-ai).

Org `.github` repos keep agent rules in `AGENTS.md` only: no `CLAUDE.md` and
no `.claude/` directory. The enterprise-wide rules are in
[`nyuchi/.github/AGENTS.md`](https://github.com/nyuchi/.github/blob/main/AGENTS.md)
and apply here in full. This file adds what is specific to `shamwari-ai`. A repo's
own `AGENTS.md` wins over both where they conflict.

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
