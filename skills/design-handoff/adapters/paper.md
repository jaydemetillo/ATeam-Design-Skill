# Adapter — Paper (paper.design)

| Concept | In Paper |
|---|---|
| Flow container | A frame/group on the canvas named after the flow (`Adding Team`), holding the artboards |
| Screen | Artboard inside the flow container |
| Screen name | Artboard name — `Team/02 Details/Filled` |
| Note | `Notes · <screen name>` text layer under each artboard |
| Container link | Paper file link + container name (deep links to a node aren't guaranteed) |
| Ready for dev / Completed | No native status → Linear ticket status |

## Setup (once per machine)
Paper's official MCP server runs locally through **Paper Desktop** — open a file and it
starts automatically on `http://127.0.0.1:29979/mcp`.

```bash
# Claude Code
claude mcp add paper --transport http http://127.0.0.1:29979/mcp --scope user
```
- **Codex**: install Paper's plugin from the `paper-design/agent-plugins` marketplace,
  or add the same URL under `[mcp_servers]` in `~/.codex/config.toml`.
- Only use the official local server. Don't use third-party wrappers that read your
  browser cookies — they handle your Paper session outside Paper's control.
- Free plan has a weekly tool-call cap; batch reads and avoid needless screenshots.

## Mechanics
- **List the tools first** — Paper's tool set changes often. Typical reads:
  `get_selection`, `get_jsx`, `get_screenshot`, `get_computed_styles`. Typical writes:
  `create_artboard`, `write_html`, `set_text_content`, `update_styles`, plus layer
  rename / reparent.
- **Read names**: ask the designer to select the flow container, `get_selection`, and
  collect its child artboards.
- **New screens**: `create_artboard` inside the container with the final
  `Feature/NN Step/State` name, then fill it.
- **Renames**: only after the user approves the rename table (§3.1).
- **No MCP connected?** Fall back to check-only: ask for the artboard names or a
  screenshot, run §3.1, and hand back the rename table. Never claim a change happened
  in Paper unless a tool confirmed it.

## Notes on each screen
Paper has no annotation feature, so the note block is the only carrier.
- **Note block**: a text layer directly under each artboard, inside the container,
  named `Notes · <screen name>`, one `[NN Step] …` line per fact.
- **From Figma**: copy each screen's block (including lines that were Figma
  annotations) into a text layer under the matching artboard.
- **From code**: copy the block from the screen's story/component (see [code.md](code.md)).
- When generating code from Paper (`get_jsx`), carry the block into the code as below.

## Existing file (§2B)
**Audit (read-only)** — ask the designer to select the page area or flow, then
`get_selection` (and `get_jsx` / `get_screenshot` if needed):
- Flows: existing frames/groups holding artboards; else rows of artboards with a large
  title text above them.
- Screens: artboards — record names; state clues from content.
- Notes: loose text layers near artboards; artboards with no `Notes · <screen name>` layer.
- Status: Paper has none — check the Linear ticket if one was given.

**Apply (only what was approved)** — use Paper's rename / reparent / create tools
(check the live tool list): rename artboards, reparent artboards into a new flow
frame, move/tag loose text into note layers. For **status**, ask "Update the Linear
ticket to Ready for dev?" — only if a Linear tool is connected; otherwise tell the
designer what to set.

**No Paper MCP connected** — run the audit from names/screenshots the designer
provides, ask the same questions, then hand back a checklist (rename table, grouping,
notes to add) for the designer to apply by hand.
