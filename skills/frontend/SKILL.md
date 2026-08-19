---
name: frontend
description: Route a frontend/UI request for the current repo to impeccable — polish, audit, critique, and the rest. Use when the user asks to audit, polish, or critique a UI in a repo that has frontend code.
---

Hand the request to the `impeccable` skill/plugin — it already covers polish, audit, critique, and every other frontend command. Don't reimplement any of its design review logic here.

Pass along whatever target or sub-command the user specified. If they didn't specify one, let impeccable's own flow infer it rather than guessing here.

If `impeccable` isn't installed or available, say so and stop. There's nothing to fall back to.
