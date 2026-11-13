# Adapter — Figma

| Concept | In Figma |
|---|---|
| Flow container | **Section** (`figma.createSection()`), named after the flow |
| Screen | Top-level frame inside the section (ignore frames under ~200px both sides) |
| Screen name | `frame.name = "Team/02 Details/Filled"` |
| Note | `Notes · <screen name>` text node under each screen (annotations mirrored into it) |
| Container link | `https://www.figma.com/design/<fileKey>/<name>?node-id=<sectionId, ":" → "-">` |
| Ready for dev | Dev Mode (Shift + D) → select section → *Mark as ready for dev*, or `section.devStatus = { type: 'READY_FOR_DEV' }` after a yes |
| Completed | Same tag's dropdown → *Completed*, or `devStatus = { type: 'COMPLETED' }` after a yes |

## Setup (once per person)
**claude.ai (OGP Team plan)** — Figma is reached through the **OGP MCP Gateway**:
1. You need claude.ai on the OGP Team plan; `design-validate` and `design-handoff` are
   published to the team and appear automatically.
2. claude.ai → Settings → Connectors → connect the **OGP MCP Gateway**.
3. In a chat, ask Claude: "turn on Figma in the OGP MCP Gateway", then sign in to Figma
   when prompted.

If Figma tools aren't available in a claude.ai chat, check the Gateway is connected and
Figma is turned on in it before anything else — don't ask the user for Figma tokens.

**Claude Code / Codex** — connect Figma's own MCP server (remote, or Figma Desktop's
local server) in the agent's MCP settings.

## Mechanics
- Load the Figma `figma-use` skill before any `use_figma` call.
- **Read names**: `get_metadata` on the section node; top-level `FRAME` children are screens.
- **Screens trapped in a big frame**: the user right-clicks the frame → *Convert to
  section*. Via the API, create a section, move the frames into it, then remove the
  empty wrapper — confirm with the user first if the wrapper has fills or content.
- **Bulk rename by hand**: select screens → Cmd + R.
- **Share**: select the section → Cmd + L copies its link (Cmd + C copies layers).
- Annotation bubbles can cover neighbouring screens; tell the designer to toggle
  them off rather than moving screens around them.
- Status is writable via `use_figma` (`devStatus`) but only after the user says yes (§1.5).

## Notes on each screen
- **Note block**: one text node directly under each screen, inside the section, named
  `Notes · <screen name>` (e.g. `Notes · Login/02 OTP/Default`), width = screen width.
  One line per fact, each starting `[NN Step]` or `[NN Step/State]`.
- **Annotations**: Dev Mode annotations may pin a line to an element, but the same line
  must also be in the screen's note block. When reading, collect both; flag any
  annotation missing from the block.
- **Reading for build**: `get_metadata` on the section → for each screen frame, the
  sibling text node `Notes · <screen name>` plus `node.annotations` on its descendants.
- **Out to code/Paper**: copy the block verbatim (see [code.md](code.md), [paper.md](paper.md)).

## Existing file (§2B)
**Audit (read-only)** — one `use_figma` read script or `get_metadata` on the page/section:
- Flows: existing `SECTION`s; else rows of top-level frames with a large text node just
  above them (a "floating title") or a shared name prefix; prototype `reactions` give order.
- Screens: top-level frames ≥ ~200px; record name, x/y, and visible state clues
  (error-coloured text, empty lists, spinners/skeletons).
- Notes: text nodes outside screens (map each to the nearest screen below/left of it),
  `node.annotations` on descendants, missing `Notes · <screen name>` blocks.
- Status: `devStatus` on each section / top-level frame.

**Apply (only what was approved)**
| Approved change | How |
|---|---|
| Wrap a flow | `figma.createSection()`, name it, size it around the screens, `appendChild` each screen (keeps absolute position); leave the old title text beside it unless told to remove |
| Screens stuck in a big frame | Ask first: "Convert *Frame 12* into a section?" → move its screens into a new section; don't delete the wrapper unless told |
| Rename screens | `frame.name = '<new>'`, batch ≤ 20 per call, return old→new |
| Notes | Move/tag existing text into `Notes · <screen name>` under each screen; copy annotation labels in as lines; never change wording beyond adding the tag/type |
| Status | `section.devStatus = { type: 'READY_FOR_DEV' }` / `'COMPLETED'` / `null`. Must be on a node directly under the page or a section, and not nested inside another node with a status — if it is, say so and ask which level to use |
