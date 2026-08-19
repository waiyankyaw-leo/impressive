---
name: frontend
description: Route a frontend/UI request for the current repo to impeccable — polish, audit, critique, and the rest. Use when the user asks to audit, polish, or critique a UI in a repo that has frontend code.
allowed-tools:
  - Bash(npx impeccable *)
---

Hand the request to the `impeccable` skill/plugin — it already covers polish, audit, critique, and every other frontend command. Don't reimplement any of its design review logic here.

Pass along whatever target or sub-command the user specified. If they didn't specify one, let impeccable's own flow infer it rather than guessing here.

If `impeccable` isn't available, don't just stop — install it first, unattended:

```
npx impeccable skills install -y --providers=<current-platform> --scope=project
```

`<current-platform>` is `claude` or `codex`, whichever this session is running on. This install is non-interactive (`-y` skips all prompts) and safe to run without asking first — same category of action as `code-review-graph install` in `setup`. Only after that install also fails should you tell the user and stop.
