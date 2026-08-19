# impressive

A Claude Code / Cowork / Codex plugin that turns a recurring, manual
per-repo setup into one command.

Every new repo needs the same three things wired up: repo-specific agents,
a code knowledge graph for impact search, and a frontend design tool. Doing
that by hand, in the same order, on every repo gets old. This plugin
packages it once and makes it available everywhere the plugin is enabled.

## Why this is worth using

- **One command instead of three tools, remembered correctly, every time.**
  `setup` runs the full sequence; the two standalone skills cover the
  common one-off follow-ups.
- **Glue, not reimplementation.** Every skill here just invokes an existing
  tool with sensible defaults. Nothing about harness, code-review-graph, or
  impeccable is duplicated — if they change, this plugin doesn't need to.
- **A real safety boundary, not a suggestion.** `setup` mutates a repo
  (writes config, installs hooks, generates files), so it's locked to
  explicit invocation only (`disable-model-invocation: true`). An agent
  can't decide on its own to bootstrap your repo — only you, typing the
  command, can.
- **Cross-platform without drift.** The two safe wrapper skills
  (`agents`, `frontend`) are literally the same files under both the
  Claude/Cowork and Codex manifests — one copy, not a fork maintained
  twice.
- **Idempotent.** Every step is safe to re-run. Re-running `setup` after a
  big refactor just re-syncs; it never duplicates config or agents.

## What it wraps

| Tool | Role |
|---|---|
| **harness** | Meta-skill that scaffolds repo-specific subagents from the codebase. |
| **[code-review-graph](https://github.com/tirth8205/code-review-graph)** | External CLI/MCP tool (installed via `pip`, on PATH). This plugin runs its own installer (`code-review-graph install --platform claude-code -y`), which wires up its MCP server, generates its own skills, and injects `CLAUDE.md` instructions. |
| **[impeccable](https://github.com/pbakaus/impeccable)** | Frontend design fluency — polish, audit, critique, and more. |

## Platforms

Ships as a native plugin for both:

- **Claude Code / Cowork** — `.claude-plugin/plugin.json`
- **Codex** — `.codex-plugin/plugin.json`

Both manifests point at the same `skills/agents` and `skills/frontend` — one
copy, no drift. `skills-claude/setup` is Claude-only (see below).

## Install

### Claude Code / Cowork

Enable this plugin, then per repo:

#### 1. Run `/impressive:setup`

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

- `/impressive:agents` — just refresh agents (e.g. after a big refactor)
- `/impressive:frontend` — route a UI request straight to impeccable

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

## License

MIT — see [LICENSE](./LICENSE).
