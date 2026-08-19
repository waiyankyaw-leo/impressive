# repo-toolkit

My standard per-repo setup, packaged as a Claude Code plugin instead of three
tools I re-invoke by hand on every repo.

Each skill here is glue, not logic — it wraps an existing tool and never
reimplements what that tool already does.

## What it wraps

- **harness** — meta-skill that scaffolds repo-specific subagents (installed
  separately, invoked here by name).
- **[code-review-graph](https://github.com/tirth8205/code-review-graph)** — an
  external CLI/MCP tool (installed via `pip`, on PATH as `code-review-graph`).
  This plugin just runs its own installer
  (`code-review-graph install --platform claude-code -y`), which wires up its
  MCP server, generates its own skills, and injects `CLAUDE.md` instructions.
- **[impeccable](https://github.com/pbakaus/impeccable)** — frontend design
  fluency (polish, audit, critique, etc.).

## Platforms

Ships as a native plugin for both:

- **Claude Code / Cowork** — `.claude-plugin/plugin.json`
- **Codex** — `.codex-plugin/plugin.json`

Both manifests point at the same `skills/agents` and `skills/frontend` — one
copy, no drift. `skills-claude/setup` is Claude-only (see below).

## Install

### Claude Code / Cowork

Enable this plugin, then per repo:

#### 1. Run `/repo-toolkit:setup`

Once per repo. It will:

- Detect whether this is a frontend repo
- Scaffold repo-specific agents via harness
- Run `code-review-graph install` for impact/graph search
- Note impeccable in `CLAUDE.md` if frontend was detected

`setup` is deliberately locked to explicit invocation only
(`disable-model-invocation: true`) — Claude cannot trigger a full repo
bootstrap on its own judgment, only you typing the command can.

#### 2. You're wired up

The other two skills are there for later, standalone:

- `/repo-toolkit:agents` — just refresh agents (e.g. after a big refactor)
- `/repo-toolkit:frontend` — route a UI request straight to impeccable

Re-running any of these is safe — every step is idempotent.

### Codex

Enable this plugin the same way. `agents` and `frontend` work identically.

**`setup` is not available on Codex.** Codex's plugin schema has no
equivalent of `disable-model-invocation` (its validator rejects it outright),
so there's no way to offer the same explicit-only guardrail there. Rather
than ship an unguarded auto-bootstrap skill, run the three steps by hand
once per repo instead:

```
code-review-graph install --platform codex -y
```

then trigger `harness` and, if the repo is frontend, `impeccable` yourself.
