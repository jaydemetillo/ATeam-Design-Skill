---
name: design-validate
description: Check a design flow before it's handed to engineering. Run as `/design-validate <section link> against <Linear ticket or PRD link | "no PRD"> [and <repo>]` (Codex: `$design-validate …`). Audits screen names against `Feature/NN Step/State` and offers to rename (only after the user says yes), reads the PRD and the repo's data model, and finds gaps — PRD not covered, empty and "lots of" states, long text, invalid input, users in several teams, failed saves. Returns at most 10 items, PM decisions first, then missing states to draw. Triggers on "/design-validate", "validate this section/flow", "is this ready for dev", or a Figma/Paper/Pencil link plus a Linear ticket or PRD.
---

# design-validate

This is the entry point for the check described in the Engineering x Design doc. The
rules it checks against live in the **design-handoff** skill — read it first:

1. Read [`../design-handoff/SKILL.md`](../design-handoff/SKILL.md) (rules §1, check §3).
2. Read the adapter for the tool the link points to
   ([Figma](../design-handoff/adapters/figma.md), [Paper](../design-handoff/adapters/paper.md),
   [Pencil](../design-handoff/adapters/pencil.md), [code](../design-handoff/adapters/code.md)).
   On claude.ai, Figma is reached through the **OGP MCP Gateway** — see the Figma
   adapter's setup section.

## Inputs
| Input | Required | Notes |
|---|---|---|
| Section link | Yes | One link to the flow container, not individual screens |
| Linear ticket or PRD link | No | Say "no PRD" if there isn't one — the check still runs |
| Repo name | No | Lets the check read the data model (schemas, enums, relations, validation) |

If the section link is missing, ask for it. Don't ask for anything else up front.

## Steps
Run design-handoff §3 in order:

1. **§3.1 Names** — list top-level screens (ignore tiny frames like icons), flag anything
   not `Feature/NN Step/State`, propose a rename table. **Rename only after the user says yes.**
   Also check every screen has its own note block (`Notes · <screen name>`), every note
   line's tag matches a screen, and every tool annotation is copied into its screen's
   block. Offer to fix, again only after yes.
2. **§3.2 Sources** — read the PRD/ticket, the data model if a repo is given, and the
   screens' notes and annotations.
3. **§3.3 Gaps** — PRD not covered, empty and "lots of", long text, invalid input, users in
   several teams, saves that fail (plus loading, destructive edges, UI vs data mismatches).
4. **§3.4 Output** — exactly this shape, 10 items max, decisions first:

```
<Flow>: <n> decisions, <m> missing states

Decisions (PM)
- <NN Step/State>: <question grounded in a PRD/data mismatch>

Missing states (design)
- <NN Step/State>: <state to draw>
```

## After the check
- Not clean: the designer draws the states, sorts out decisions with the PM, and runs
  `/design-validate` again.
- Clean: say so, then **ask** "Mark *<Flow>* as Ready for dev?" Set it only after a
  yes (Figma: `devStatus`; other tools: Linear — see the adapter), then remind the
  designer to paste the section link into the Linear ticket.
- Existing file with names, groups or notes to fix: don't fix them silently — run the
  design-handoff §2B questions (flows → feature word → renames → notes → status).
