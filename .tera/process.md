This is this repository's process profile. Every session the dev pipeline
dispatches here reads it before anything else, and where it speaks to a
matter of this repository it outranks the session's own defaults. Each
choice below is followed by its reason.

## Gate

```sh
make check
```

- **This repository already has a single command that builds, vets and
  tests, and CI runs the same command.** *Why:* `Makefile` defines `check` as
  `go vet ./...` then `go test ./...`, and `.github/workflows/ci.yml` runs
  `make check` as its `check` job — a session, a person and CI already agree
  on what green means, so the template's `scripts/gate.sh` would only
  duplicate it.
- **Run it before opening a pull request** (`CLAUDE.md`, `AGENTS.md`), and
  CI runs it again as the required check.
- **A red the change did not cause is named in the pull request, with how
  that was shown, and is not fixed in the same pull request.** *Why:* one
  pull request stays one item.

## Build and test

- **Build and test through the steps `make check` runs**: `go vet ./...`,
  then `go test ./...`. See `Makefile`.
- **Every exported function has a table-driven test beside it.**
  (`CLAUDE.md`)
- **Keep the package dependency-free: the standard library only.**
  (`CLAUDE.md`, `AGENTS.md`)
- **A flaky test is investigated, never re-run until it passes, and never
  fixed by loosening its assertion or raising a timeout.** *Why:* a retried
  flake hides a real race.

## Branches and pull requests

- **Branch from the up-to-date `main`, named `<type>/<short-description>`,
  one pull request per change.** (`CONTRIBUTING.md`)
- **Title pull requests `<type>(<scope>): <change>`, Conventional Commits
  style.** (`CONTRIBUTING.md`)
- **The body follows `.github/pull_request_template.md`: what changed, why,
  how it was verified.**
- **Keep the branch up to date with `main` before merging.** *Why:* the
  required check on `main` is strict, so a branch behind `main` cannot merge
  until it is brought up to date.

## Review and merge

- **A maintainer named in `.github/CODEOWNERS` approves every pull
  request.** Branch protection on `main` requires 1 approving review with
  code-owner review enforced.
- **The required check is CI's `check` job**, which runs `make check`.
- **Pull requests are squash-merged; the pull request title becomes the
  commit subject.** (`CONTRIBUTING.md`; confirmed by merged history — each
  merge commit's subject is its pull request's title.)

## CI

- **CI runs `make check` on every pull request and every push to `main`**,
  in `.github/workflows/ci.yml`, job `check`.
- **A session does not start, wait on or re-run the default branch's CI.**
  *Why:* that run belongs to what has already merged.
- **A check is never disabled, skipped or marked optional to get a merge
  through.**

## Naming

- **Pull requests:** `<type>(<scope>): <change>`, Conventional Commits.
  (`CONTRIBUTING.md`)
- **Work Orders:** `<feature> [<item>]: <action>`, for example
  `tally [W1-C2]: add a byte count`. Work outside a plan drops the address.
  *Why:* a plan's items stay findable by the feature's slug and the item's
  address.
- **Cohorts:** a plan's cohort is `<feature> - <short description>`, and
  each wave beneath it is `<feature> - wave <N> - <short description>`.
  *Why:* these are the labels the dev pipeline's orchestrators open.

## Documents

- **Plans live in `docs/plans/`.** *Why:* the dev pipeline's orchestrators
  drive a plan wave by wave, and a session told where plans live reads one
  rather than inventing it. No other document kind has a home in this
  repository yet; add one here when it does.
- **A document is changed by pull request, like code.**

## People

- **Decides:** the maintainers decide requirements and designs. They are the
  people `.github/CODEOWNERS` names (`@efunneko`). *Why:* one named group
  decides, so a design never waits on someone who was never asked.
- **Ratifies:** a maintainer ratifies a plan before its train starts, and
  ratifies its documents' status once the train is done. *Why:* a train
  runs only on a plan a person has accepted.
- **Out-of-band acts:** whatever a session cannot do, such as a release, a
  credential or a deployment, is done by a maintainer and filed as off-tera
  work.

## Rules

- **Never commit a secret**: no token, password or key in a file, a commit
  message or a pull request.
- **Never push to `main` directly.** Every change goes through a pull
  request.
- **Stage files by name, never with `git add -A` or `git add .`.** *Why:* a
  bulk add commits whatever else is in the tree.
- **Keep the package dependency-free: the standard library only.**
  (`CLAUDE.md`, `AGENTS.md`)
- **Ask before anything destructive or hard to undo**: deleting a branch
  someone else uses, rewriting `main`'s history, or changing this profile or
  the CI workflow.
