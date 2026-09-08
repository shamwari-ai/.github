## What this changes

<!-- One or two sentences. The PR title carries the Conventional Commit
     type; this is the part a reviewer reads first. -->

## Why

<!-- Reasoning the diff cannot express. If this fixes an issue, link it:
     "Closes #123". If it is a follow-up, link the PR it follows. -->

## Rule 1 and rule 2

<!-- Delete this section only if the change touches neither. -->

- [ ] This change does **not** let `personal`-scope data reach a
      third-party inference provider.
- [ ] This change does **not** alter `licenseClass` handling, or if it
      does, the premium tier still resolves to `restricted`.
- [ ] The scope gate is still implemented independently in
      `gateway/src/scope.ts` **and** `core/main.py::resolve_scope` — this
      PR does not collapse them into a shared helper.

> The duplication is deliberate. See `CLAUDE.md` in `shamwari-ai/shamwari`
> and "Do not put the scope gate in a shared package" in the repo-split
> plan.

## Checks

- [ ] CI passes.
- [ ] PR title follows Conventional Commits (`feat:`, `fix:`, `docs:`, …),
      subject lowercase and imperative, no trailing period.
- [ ] If dependencies changed in `core/`, `requirements.txt` was
      regenerated with `pip-compile --generate-hashes` and **not** hand-edited.
- [ ] If user-facing copy changed, it follows the language-discipline rules
      (for example "open weights", never "open source model").

## Deployment

<!-- Does this need a `wrangler deploy`, a migration, or a secret set?
     Say so explicitly. "No" is a fine answer. -->
