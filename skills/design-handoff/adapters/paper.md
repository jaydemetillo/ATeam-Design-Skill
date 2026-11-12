# Adapter — Paper (paper.design)

| Concept | In Paper |
|---|---|
| Flow container | A frame/group on the canvas named after the flow (`Adding Team`), holding the artboards |
| Screen | Artboard inside the flow container |
| Screen name | Artboard name — `Team/02 Details/Filled` |
| Note | Text layer under the artboard, inside the container, starting `[NN Step]` |
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
