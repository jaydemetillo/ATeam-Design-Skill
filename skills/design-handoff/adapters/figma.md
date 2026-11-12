# Adapter — Figma

| Concept | In Figma |
|---|---|
| Flow container | **Section** (`figma.createSection()`), named after the flow |
| Screen | Top-level frame inside the section (ignore frames under ~200px both sides) |
| Screen name | `frame.name = "Team/02 Details/Filled"` |
| Note | Text node under the screen, inside the section. Dev Mode annotations also OK |
| Container link | `https://www.figma.com/design/<fileKey>/<name>?node-id=<sectionId, ":" → "-">` |
| Ready for dev | Dev Mode (Shift + D) → select section → *Mark as ready for dev* |
| Completed | Same tag's dropdown → *Completed* (engineer) |

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
- Ready for dev isn't writable through the MCP — the designer always sets it.
