# Adapter — Pencil (`.pen`)

| Concept | In Pencil |
|---|---|
| Flow container | Top-level frame on the canvas named after the flow, holding the screens |
| Screen | Direct child frame of the flow container |
| Screen name | The node's name — `Team/02 Details/Filled` |
| Note | Text node under the screen inside the container, starting `[NN Step]` |
| Container link | The `.pen` file path + container node name (no deep links) |
| Ready for dev / Completed | No native status → Linear ticket status, with the container name in the ticket |

## Mechanics
- `.pen` files are encrypted — only touch them through the pencil MCP, never read them as files.
- **Read names**: `batch_get` / `snapshot_layout` on the container; its child frames are screens.
- **Place new screens**: `find_empty_space_on_canvas` inside the container, then
  `batch_design`, setting the name in the same operation.
- **Bulk rename**: `batch_design` with name updates — only after the user approves the rename table.
- Read `get_guidelines` first for the file's own layout conventions.
