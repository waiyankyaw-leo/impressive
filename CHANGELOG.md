# Changelog

## 1.4.1

Bug fixes from a code review of the accumulated changes:

- `frontend`'s `allowed-tools` was missing `Skill`, so it could only run
  its Bash install fallback and could never actually invoke `impeccable`
  in the common case where it was already installed. This broke the
  skill's entire primary purpose — fixed by adding `Skill` back and by
  making the flow explicit: try the Skill invocation first, install on
  failure, then retry once.
- `setup`'s heading still said "repo-toolkit", the plugin's name before
  the rename to "impressive".
- `setup`'s `code-review-graph` availability check assumed a POSIX
  shell (`command -v`); now names the PowerShell equivalent too.

## 1.4.0

- `frontend` now self-installs impeccable when it's missing, instead of
  just stopping. Runs `npx impeccable skills install -y --providers=<platform>
  --scope=project` (unattended, no prompts) before routing the request —
  matters most on Codex, where impeccable isn't reachable via
  `codex plugin add`.
- README corrected: Codex's Codex section previously said the three
  standalone skills "work identically" to Claude Code, which wasn't true.
  Now documents per-skill what actually happens on Codex, including that
  `harness` has no Codex support at all and what Codex's own native
  subagent system offers instead.

## 1.3.0

- Added `visualize`, wrapping
  [diagram-design](https://github.com/cathrynlavery/diagram-design) (MIT,
  28 editorial diagram types as standalone HTML/SVG). Shared between Claude
  and Codex, same as `agents`/`frontend` — diagram-design already ships its
  own dual-platform manifest, so no guardrail conflict.

## 1.2.0

- `setup` now checks `code-review-graph` is on PATH before running its
  installer, instead of letting the command fail with a raw shell error.
- `frontend` now checks `impeccable` is available before routing to it,
  matching the guard `agents` already had for `harness`.
- Added `.gitattributes` (`eol=lf`) to stop line-ending churn on every
  commit.

## 1.1.0

- Added native Codex support (`.codex-plugin/plugin.json`).
- Split `setup` out into `skills-claude/` — Codex's plugin schema has no
  equivalent of `disable-model-invocation`, so the explicit-invocation
  guardrail can't be offered there. `agents` and `frontend` stay shared
  between Claude and Codex.
- Renamed the plugin `repo-toolkit` → `impressive`.

## 1.0.0

- Initial release: `setup`, `agents`, `frontend` skills wrapping harness,
  code-review-graph, and impeccable for Claude Code / Cowork.
