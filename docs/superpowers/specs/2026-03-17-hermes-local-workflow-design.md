# Hermes Local Workflow Design

**Date:** 2026-03-17

## Goal

Set up Hermes so local improvements are easy to make without turning future upstream updates into merge cleanup work.

## Current Context

- Local checkout: `/Users/wang/github/ifrederico/hermes-agent`
- Current fork remote: `origin = ifrederico/hermes-agent`
- Official upstream remote should be `NousResearch/hermes-agent`
- User wants long-lived reference codebases that an LLM can inspect for inspiration while features are being implemented

## Decisions

### 1. Keep `main` clean

`main` should remain close to `upstream/main`. Real work should not happen directly on `main`.

### 2. Use worktrees for active tasks

Each fix or feature should use its own branch and its own worktree under `.worktrees/`. This keeps the base checkout clean and avoids repeated branch switching in a single directory.

### 3. Separate product code from reference code

Reference repositories should not live inside the Hermes repository and should not use `/tmp`. They should live in a stable sibling directory so they survive restarts and remain easy to inspect.

Recommended location:

- `/Users/wang/github/reference-codebases/`

### 4. Configure local git defaults around the fork workflow

The local repository should:

- fetch from `upstream`
- prefer fast-forward pulls
- push to `origin` by default
- treat `main` as tracking `upstream/main`

This preserves a clean update path while keeping the fork as the default push destination.

## Directory Layout

```text
/Users/wang/github/
├── ifrederico/hermes-agent/              # stable base checkout
│   └── .worktrees/
│       └── <task-name>/                  # active isolated feature/fix work
└── reference-codebases/
    ├── INDEX.md
    ├── <repo-1>/
    ├── <repo-2>/
    └── ...
```

## Daily Workflow

1. Update the base checkout on `main` from `upstream/main`.
2. Create a new worktree and branch for the task.
3. Read reference repos from `reference-codebases/` as needed.
4. Implement and test inside the task worktree.
5. Merge or rebase the task branch when ready.

## Non-Goals

- No attempt to automate cloning of specific reference repos yet
- No global git config changes outside this repository
- No changes to Hermes runtime behavior
