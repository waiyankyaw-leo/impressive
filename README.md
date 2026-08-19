# impressive

A Claude Code / Cowork / Codex plugin that turns a recurring, manual
per-repo setup into one command.

Every new repo needs the same things wired up: repo-specific agents, a code
knowledge graph for impact search, a frontend design tool, and a way to
produce real diagrams. Doing that by hand, in the same order, on every repo
gets old. This plugin packages it once and makes it available everywhere
the plugin is enabled.

## Why this is worth using

- **One command instead of several tools, remembered correctly, every time.**
  `setup` runs the core sequence; the standalone skills cover the common
  one-off follow-ups.
- **Glue, not reimplementation.** Every skill here just invokes an existing
  tool with sensible defaults. Nothing about harness, code-review-graph,
  impeccable, or diagram-design is duplicated — if they change, this
  plugin doesn't need to.
- **A real safety boundary, not a suggestion.** `setup` mutates a repo
  (writes config, installs hooks, generates files), so it's locked to
  explicit invocation only (`disable-model-invocation: true`). An agent
  can't decide on its own to bootstrap your repo — only you, typing the
  command, can.
- **Cross-platform without drift.** The safe wrapper skills
  (`agents`, `frontend`, `visualize`) are literally the same files under
  both the Claude/Cowork and Codex manifests — one copy, not a fork
  maintained twice.
- **Idempotent.** Every step is safe to re-run. Re-running `setup` after a
  big refactor just re-syncs; it never duplicates config or agents.

## What it wraps

| Tool | Role |
|---|---|
| **harness** | Meta-skill that scaffolds repo-specific subagents from the codebase. |
| **[code-review-graph](https://github.com/tirth8205/code-review-graph)** | External CLI/MCP tool (installed via `pip`, on PATH). This plugin runs its own installer (`code-review-graph install --platform claude-code -y`), which wires up its MCP server, generates its own skills, and injects `CLAUDE.md` instructions. |
| **[impeccable](https://github.com/pbakaus/impeccable)** | Frontend design fluency — polish, audit, critique, and more. |
| **[diagram-design](https://github.com/cathrynlavery/diagram-design)** | 28 editorial diagram types (architecture, flowchart, sequence, ER, timeline, and more) as standalone HTML/SVG. Also redraws existing Mermaid/drawio sources. |

## Requirements

This plugin is glue — the actual work happens in the tools it wraps, so
what you need depends on which skills you use:

| Requirement | Needed for | Why |
|---|---|---|
| Git | `setup` | It checks the repo is a git repo before doing anything (`git status`). |
| Python 3.10+ and `pip` | `setup` (code-review-graph step) | `code-review-graph` installs via `pip install code-review-graph`. |
| [uv](https://docs.astral.sh/uv/) (provides `uvx`) | `setup` (code-review-graph step) | code-review-graph's generated `.mcp.json` launches its MCP server via `uvx code-review-graph serve` — installing the pip package alone isn't enough, `uvx` has to be on PATH too. |
| Node.js 22.18+ and `npx` | `frontend` | impeccable's CLI (`npx impeccable ...`) requires it. |
| harness | `agents`, `setup` | Separate Claude Code plugin — install it yourself; not installed by this plugin. Claude-only, no Codex support. |
| diagram-design | `visualize` | Separate plugin, dual-platform. `visualize` doesn't auto-install it — add it yourself if missing (`/plugin install diagram-design@diagram-design` on Claude, `codex plugin add diagram-design@diagram-design` on Codex). |
| Claude Code CLI, Cowork, or Codex CLI | everything | Whichever platform you're installing `impressive` into. |

`agents` and `visualize` don't self-install their underlying tool if it's
missing — they just say so and stop. `frontend` is the one exception: it
asks before installing impeccable for you (see below).

## Platforms

Ships as a native plugin for both:

- **Claude Code / Cowork** — `.claude-plugin/plugin.json`
- **Codex** — `.codex-plugin/plugin.json`

Both manifests point at the same `skills/agents`, `skills/frontend`, and
`skills/visualize` — one copy, no drift. `skills-claude/setup` is
Claude-only (see below).

## Install

### Claude Code / Cowork

From inside a session:

```
/plugin marketplace add waiyankyaw-leo/impressive
/plugin install impressive@impressive-marketplace
```

Or from the CLI:

```bash
claude plugin marketplace add waiyankyaw-leo/impressive
claude plugin install impressive@impressive-marketplace
```

Then, per repo:

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

The other skills are there for later, standalone:

- `/impressive:agents` — just refresh agents (e.g. after a big refactor)
- `/impressive:frontend` — route a UI request straight to impeccable
- `/impressive:visualize` — route a diagram request straight to diagram-design

Re-running any of these is safe — every step is idempotent.

### Codex

```bash
codex plugin marketplace add waiyankyaw-leo/impressive
codex plugin add impressive@impressive-marketplace
```

The three skills don't all carry over the same way — checked against what
each underlying tool actually ships for Codex, not assumed:

**`agents` won't do anything on Codex, and that's fine.** `harness` (what it
wraps) has zero Codex support — its repo only ships `.claude-plugin`. But
this isn't really a gap: Codex has its own built-in subagent system that
doesn't need repo-specific pre-scaffolding the way harness provides for
Claude. Three generic agents (`default`, `worker`, `explorer`) ship out of
the box and spawn on request — "spawn two agents," "delegate this in
parallel" — no generation step required. If you want genuinely
codebase-specific custom agents on Codex, that's a `.codex/agents/*.toml`
file per agent (`name`, `description`, `developer_instructions`) — much
lighter than harness's meta-skill approach, worth doing by hand if you need
it, but not something this plugin automates today. ([official
docs](https://learn.chatgpt.com/docs/agent-configuration/subagents))

**`frontend` self-installs impeccable on first use.** impeccable ships
partial Codex support (a `.codex/hooks.json`) but installs via its own
`npx impeccable` CLI, not through `codex plugin add`. `frontend` handles
this itself — if impeccable isn't found, it runs
`npx impeccable skills install -y --providers=codex --scope=project`
(fully unattended, no prompts) before routing the request. Nothing extra
for you to run first.

**`visualize` works normally.** diagram-design ships a real
`.codex-plugin/plugin.json`, so `codex plugin add diagram-design@diagram-design`
is all it needs.

**`setup` is not available on Codex at all.** Codex's plugin schema has no
equivalent of `disable-model-invocation` (its validator rejects it outright),
so there's no way to offer the same explicit-only guardrail there. Run the
underlying step by hand instead:

```
code-review-graph install --platform codex -y
```

## License

MIT — see [LICENSE](./LICENSE).
