# Hermes Local Workflow Bootstrap Implementation Plan

> **For agentic workers:** REQUIRED: Use superpowers:subagent-driven-development (if subagents available) or superpowers:executing-plans to implement this plan. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Configure a clean fork-and-worktree workflow for Hermes and create a stable home for long-lived reference codebases.

**Architecture:** The base Hermes checkout remains the sync point for `main`, active work happens in `.worktrees/` branches, and reference repositories live outside Hermes in a sibling directory. Local git config is adjusted only for this repository so the workflow is safe by default without affecting unrelated repos.

**Tech Stack:** Git, git worktrees, local repository config, Markdown docs

---

## Chunk 1: Repository Bootstrap

### Task 1: Configure git remotes and local defaults

**Files:**
- Modify: `.git/config`
- Create: `docs/superpowers/specs/2026-03-17-hermes-local-workflow-design.md`
- Create: `docs/superpowers/plans/2026-03-17-hermes-local-workflow-bootstrap.md`

- [ ] **Step 1: Ensure an isolated task branch exists**

Run: `git worktree add .worktrees/dev-workflow-setup -b chore-dev-workflow-setup`
Expected: a new worktree exists at `.worktrees/dev-workflow-setup`

- [ ] **Step 2: Add the official upstream remote**

Run: `git remote add upstream https://github.com/NousResearch/hermes-agent.git`
Expected: `git remote get-url upstream` returns the NousResearch URL

- [ ] **Step 3: Fetch upstream refs**

Run: `git fetch upstream`
Expected: `upstream/main` and other upstream branches appear locally

- [ ] **Step 4: Configure repository-local defaults**

Run: `git config --local pull.ff only`
Run: `git config --local remote.pushDefault origin`
Run: `git config --local branch.main.remote upstream`
Run: `git config --local branch.main.merge refs/heads/main`
Run: `git config --local fetch.prune true`
Expected: local git config prefers upstream pulls, origin pushes, and fast-forward syncs

## Chunk 2: Reference Workspace

### Task 2: Create a stable reference-codebase root

**Files:**
- Create: `/Users/wang/github/reference-codebases/INDEX.md`

- [ ] **Step 1: Create the directory**

Run: `mkdir -p /Users/wang/github/reference-codebases`
Expected: the directory exists outside the Hermes repository

- [ ] **Step 2: Add an index file**

Create `INDEX.md` with:
- purpose of the directory
- conventions for adding reference repos
- a short list format for active references

- [ ] **Step 3: Verify visibility**

Run: `ls -la /Users/wang/github/reference-codebases`
Expected: directory contents are visible and stable across sessions

## Chunk 3: Verification

### Task 3: Verify the workflow state

**Files:**
- None

- [ ] **Step 1: Verify worktree layout**

Run: `git worktree list`
Expected: base checkout and `.worktrees/dev-workflow-setup` are both present

- [ ] **Step 2: Verify remote wiring**

Run: `git remote -v`
Expected: both `origin` and `upstream` are configured

- [ ] **Step 3: Verify main tracking**

Run: `git config --get branch.main.remote`
Run: `git config --get branch.main.merge`
Expected: `upstream` and `refs/heads/main`

- [ ] **Step 4: Run a representative test command before closing the branch**

Run: `source /Users/wang/github/ifrederico/hermes-agent/.venv/bin/activate && python -m pytest tests/test_model_tools.py -q`
Expected: passing targeted verification in the worktree
