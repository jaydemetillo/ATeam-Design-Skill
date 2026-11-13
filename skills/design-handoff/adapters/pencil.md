# Adapter — Pencil (`.pen`)

| Concept | In Pencil |
|---|---|
| Flow container | Top-level frame on the canvas named after the flow, holding the screens |
| Screen | Direct child frame of the flow container |
| Screen name | The node's name — `Team/02 Details/Filled` |
| Note | `Notes · <screen name>` text node under each screen |
| Container link | The `.pen` file path + container node name (no deep links) |
| Ready for dev / Completed | No native status → Linear ticket status, with the container name in the ticket |

## Mechanics
- `.pen` files are encrypted — only touch them through the pencil MCP, never read them as files.
- **Read names**: `batch_get` / `snapshot_layout` on the container; its child frames are screens.
- **Place new screens**: `find_empty_space_on_canvas` inside the container, then
  `batch_design`, setting the name in the same operation.
- **Bulk rename**: `batch_design` with name updates — only after the user approves the rename table.
- Read `get_guidelines` first for the file's own layout conventions.

## Notes on each screen
- **Note block**: a text node directly under each screen, inside the container, named
  `Notes · <screen name>`, one `[NN Step] …` line per fact.
- Pencil has no annotations — copy any Figma annotations into the block when importing.

## Existing file (§2B)
**Audit (read-only)** — `get_editor_state`, then `batch_get` / `snapshot_layout` on the
page or container:
- Flows: top-level frames holding screens; else rows of frames with a title text above.
- Screens, notes: as Figma — names, loose text near screens, missing `Notes · …` nodes.
- Status: none in Pencil — Linear.

**Apply (only what was approved)** — `batch_design` for renames, reparenting screens into
a new flow frame, and creating/moving note text nodes; one flow per call. Status: ask
before suggesting a Linear change.
