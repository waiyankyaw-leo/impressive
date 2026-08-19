---
name: visualize
description: Route a diagram/visualization request for the current repo to diagram-design — architecture, flowchart, sequence, ER, timeline, and other editorial diagram types as standalone HTML/SVG. Use when the user asks for an architecture diagram, flowchart, or any other diagram of this repo's structure.
---

Hand the request to the `diagram-design` skill/plugin — it already covers all 28 diagram types (architecture, flowchart, sequence, state machine, ER, timeline, swimlane, and more) as self-contained HTML/SVG, plus redrawing existing Mermaid/drawio sources. Don't reimplement any of its layout or rendering logic here.

Pass along whatever diagram type, target, or source file the user specified. If they didn't specify a type, let diagram-design's own flow infer the right one rather than guessing here.

If `diagram-design` isn't installed or available, say so and stop. There's nothing to fall back to.
