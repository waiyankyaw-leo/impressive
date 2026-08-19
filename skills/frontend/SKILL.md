---
name: frontend
description: Route a frontend/UI request for the current repo to impeccable — polish, audit, critique, and the rest. Use when the user asks to audit, polish, or critique a UI in a repo that has frontend code.
allowed-tools:
  - Skill
  - Bash(npx impeccable *)
---

First, try invoking the `impeccable` skill/plugin directly via the Skill tool — it already covers polish, audit, critique, and every other frontend command. Don't reimplement any of its design review logic here.

Pass along whatever target or sub-command the user specified. If they didn't specify one, let impeccable's own flow infer it rather than guessing here.

If that invocation fails because `impeccable` isn't available, don't just stop — but ask the user before installing it. Installing a new tool into their repo is a distinct action from routing a UI request, and needs its own explicit go-ahead even though the command itself is non-interactive:

```
npx impeccable skills install -y --providers=<current-platform> --scope=project
```

`<current-platform>` is `claude` or `codex` for this plugin's two supported platforms; if running somewhere else impeccable itself supports (Cursor, Gemini, etc. — see its own docs), use that provider's name instead. Once the user confirms, this install runs unattended (`-y` skips all further prompts). After installing, retry the Skill tool invocation once. Only if that also fails should you tell the user and stop.
