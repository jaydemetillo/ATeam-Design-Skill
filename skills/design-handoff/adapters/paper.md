# Adapter — Paper (paper.design)

| Concept | In Paper |
|---|---|
| Flow container | Its grouping feature (page, group or frame) named after the flow |
| Screen | Artboard inside the flow container |
| Screen name | Artboard name — `Team/02 Details/Filled` |
| Note | Text under the artboard, starting `[NN Step]` |
| Container link | Share link to the page/group, if Paper offers one; otherwise file link + container name |
| Ready for dev / Completed | No native status → Linear ticket status |

## Mechanics
- **Check what access exists first.** If a Paper MCP or API is connected, use it to
  list and rename artboards. If not, the agent can only **check**: ask the designer
  to paste the artboard names (or a screenshot), run §3.1 on them, and hand back the
  rename table for the designer to apply by hand.
- Never claim a rename happened in Paper unless a tool confirmed it.
- This adapter is a starting point — update it once the team confirms how Paper
  groups artboards and whether it has an MCP.
