# Fork Workflow

This repo does not use the default "fork stays close to upstream main" workflow.

Use these rules unless the user explicitly says otherwise.

## Source Of Truth

- `origin` is `ifrederico/hermes-agent`
- `upstream` is `NousResearch/hermes-agent`
- Push only to `origin`
- Never push to `upstream`

## Branch Roles

- `main` is the fork's product branch and the default base for new work
- `deploy` is the branch the production server runs
- `upstream/main` is a read-only reference branch for selective syncs

Do not assume local `main` should track `upstream/main`. In this repo, `main`
tracks `origin/main`.

## Daily Workflow

Start new work from `main`, preferably in a worktree:

```bash
git checkout main
git pull --ff-only
git worktree add .worktrees/fix-<topic> -b fix-<topic>
```

Rules:
- do not develop directly on `deploy`
- do not do real feature work on detached heads
- do not make ad hoc server-only edits unless the user explicitly asks

## Deploy Workflow

The stable server checkout should run `origin/deploy`, not `upstream/main` and
not an arbitrary feature branch.

Promotion flow:

1. Develop and test on a feature branch or worktree
2. Merge the finished work into `main`
3. Promote selected changes from `main` to `deploy`
4. Update the server from `origin/deploy`

If a change is not ready for production, keep it off `deploy`.

## Upstream Sync Workflow

Upstream changes are opt-in.

Fetch first:

```bash
git fetch upstream
git log --oneline main..upstream/main
```

Then choose one of these:
- cherry-pick specific upstream commits into `main`
- create a temporary integration branch from `main` and merge `upstream/main`
- test the result before promoting anything to `deploy`

Do not pull `upstream/main` directly into `deploy`.

## Server Expectations

The production checkout is expected to use:
- `origin` for fetch and push
- `upstream` as read-only reference
- `deploy` as the checked-out branch

Unknown live-server drift should be treated as suspicious until reviewed. Do not
silently carry forward uncommitted server changes.

## Scratch And Reference Directories

Use repo-local `tmp/` for short-lived Hermes scratch files.

Use `/Users/wang/github/reference-codebases/` for longer-lived reference repos
that agents may inspect for inspiration.

Do not store long-lived reference repos inside this git repository.
