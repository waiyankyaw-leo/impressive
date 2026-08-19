---
name: agents
description: (Re)scaffold repo-specific subagents for the current repo via harness, without running the full repo-toolkit setup. Use when the user wants agents refreshed after a big change to the codebase, or set up on a repo that already has code-review-graph and impeccable wired up.
---

Invoke the `harness` skill/plugin against the current repo to scaffold or refresh its repo-specific subagents. Don't reimplement harness's logic here — trigger it, then report what it produced.

If `harness` isn't installed or available, say so and stop. There's nothing to fall back to.
