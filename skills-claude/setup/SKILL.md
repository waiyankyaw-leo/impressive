---
name: setup
description: Bootstrap this repo with the standard toolchain — repo-specific agents via harness, impact/graph search via code-review-graph, and a frontend note pointing at impeccable if this is a UI repo. Run once per repo, before first use of the other two skills.
disable-model-invocation: true
---

# Setup repo-toolkit

Wires up three independent tools for this repo. Each step just invokes an existing tool — never reimplement what it already does. This skill is glue, not logic.

## Process

### 1. Explore

Look at the current repo before doing anything:

- `git status` — confirm this is a git repo. If not, ask before proceeding (harness and code-review-graph both assume one).
- `package.json`, `src/`, or other frontend markers (React/Vue/Svelte deps, a `components/` dir) — decides whether step 4 applies.
- `CLAUDE.md` at the repo root — does it exist? Does it already have a line pointing at impeccable? Don't duplicate it.
- `.mcp.json` and `CLAUDE.md`'s existing content — has `code-review-graph install` already run here? If so, step 3 just confirms it's current rather than re-explaining what it does.

### 2. Scaffold repo-specific agents

Invoke the `harness` skill/plugin for this repo — it defines specialist subagents from the codebase. If `harness` isn't installed or available, tell the user and skip this step. Don't try to replicate what it does.

### 3. Wire up code-review-graph

Run in the repo root:

```
code-review-graph install --platform claude-code -y
```

This is a real external CLI (not a Claude skill) — it writes its own `.mcp.json`, injects graph instructions into `CLAUDE.md`, and generates its own skills for search/impact/query. Confirm the command succeeded; don't re-implement any of what it sets up.

### 4. Note frontend tooling

Only if step 1 found frontend markers. Append one line to the repo's `CLAUDE.md` (create it if step 1 found none) pointing at `impeccable` for UI/design work. If the note is already there, skip.

### 5. Report

Tell the user what ran, what was skipped, and why (e.g. "harness not installed", "no frontend detected — skipped the impeccable note"). Re-running this skill later is safe — every step above is idempotent.
