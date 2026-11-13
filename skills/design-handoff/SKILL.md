---
name: design-handoff
description: Engineering x Design handoff conventions for any design or UI tool — Figma, Pencil (.pen), Paper, Storybook/code, or anything else Claude or Codex builds screens in. MANDATORY whenever the agent creates, edits, renames, organises, reads-for-build, or reviews screens. Enforces one container per flow, screen names in `Feature/NN Step/State`, notes tagged `[NN Step] …` under their screen, and a Ready-for-dev status set only after the user says yes. In an existing file it audits first and asks before renaming screens, grouping flows, moving notes or changing status. Also runs design-validate — naming audit, PRD/data-model coverage, missing states (empty, many, long text, invalid input, multi-team users, failed saves) — max 10 items, PM decisions first. Triggers on any design-tool write, "validate this flow", "/design-validate", "is this ready for dev", "hand this off", "rename the screens", "organise this flow", "tidy up this file", "get this ready for dev", or a design link plus a Linear ticket or PRD.
---

# Design Handoff (Engineering x Design)

Each flow lives in its own **container**, screens are named a set way, the agent
checks it, the designer marks **Ready for dev**, engineers build from one link
and mark **Completed**.

These rules apply to **every** screen the agent creates or edits — not only when
asked to validate. A screen left unnamed or outside its flow container is a bug.

## Step 0 — Pick the adapter

The rules below are tool-agnostic. Before acting, identify the tool and read its
adapter for the exact mechanics:

| You are working in… | Read |
|---|---|
| Figma (any Figma MCP, figma.com link) | [adapters/figma.md](adapters/figma.md) |
| Pencil (`.pen` file, pencil MCP) | [adapters/pencil.md](adapters/pencil.md) |
| Paper (paper.design) | [adapters/paper.md](adapters/paper.md) |
| Code — Storybook stories, React screens, routes | [adapters/code.md](adapters/code.md) |
| Running as Codex (setup, invocation, differences) | [adapters/codex.md](adapters/codex.md) — then the tool adapter above |
| Anything else | Map each concept in §1 to the closest feature; if one has no equivalent, say so and use Linear for status |

---

## 1. Rules

### 1.1 One container per flow
- Every flow is one **flow container** (Figma section, Pencil frame, Storybook
  group…), named after the flow in Title Case: `Adding Team`, `Checkout`.
- The flow's screens **and** notes live inside it. Never use a loose text node as a
  flow's "title".
- Containers may nest (a page-level container holding flow containers).
- New flow → create the container first, then build inside it. New screen in an
  existing flow → put it in that flow's container.
- Lay screens out left → right in step order; never rely on layer order to
  express flow order — the name does that.

### 1.2 Screen names: `Feature/NN Step/State`
Example: `Team/03 Add users/One selected`

| Part | Rule |
|---|---|
| **Feature** | Identical for every screen in the container. Short noun, Title Case. |
| **NN Step** | Two-digit number + short step name: `01 Start`, `03 Add users`. Same NN = same step, different states. |
| **State** | The version of the step: `Default`, `Empty`, `Filled`, `Error`, `Loading`, `Success`, `One selected`, `Many selected`, `Long text`… Sentence case. |

- Applies to top-level **screens** only — ignore icons, components and nested layers.
- No duplicates. `manage-inventory-1`, `Frame 123`, `Copy`, trailing numbers and
  kebab-case are violations.
- Same screen at two breakpoints: add a final segment —
  `Team/03 Add users/One selected/Mobile` — so prefix filters (`Team/03*`) still work.
- Exploration variants: suffix a valid name —
  `Team/03 Add users/One selected — Variant A`. Drop the suffix once chosen for dev.

### 1.3 Notes live on each screen
Every screen carries its **own note block**, directly under it, inside the container.
Notes never live in a separate file or a shared notes area — wherever the screen goes
(Figma, Paper, Pencil, code), its note block goes with it under the same screen name.

Each line starts with the step tag, so it reads the same in every tool:
```
[04 Review] This list can scroll, don't limit it to 3 people
[04 Review/Choose admin] Interaction · Admin picker: only current members are listed
```
- `[NN Step]` for the whole step, `[NN Step/State]` for one screen — as in the doc.
- Optional `Type · Element:` after the tag, so engineers can scan by kind. Types:
  **Interaction · Validation · Data · A11y · Edge case · Copy · Motion**.
- One fact per line. Write what isn't visible: behaviour, limits, rules, data source.
- A note must never overlap another screen.
- **Tool annotations** (e.g. Figma Dev Mode) are fine for pinning a note to one
  element, but every annotation must also appear as a line in that screen's note
  block — annotations don't survive the move to Paper, Pencil or code; the block does.

### 1.4 Carrying notes across tools
Whenever a screen is moved or rebuilt in another tool, copy its note block with it —
verbatim, same tags. See each adapter's "Notes" section for where the block goes.
- Design → code: the block goes into that screen's story/component (see code adapter).
- Code → design: the block in code becomes the text note under the screen.
- Engineers may add lines (`[02 OTP] Built as… / Changed because…`); agents never
  delete another person's line, only add or flag it.

### 1.5 Sharing & status
- Share **one link to the container**, never a list of screen links. When reporting
  work, always give the container link.
- **Ready for dev** is the designer's call. The agent never sets it on its own; it
  **asks** ("Mark *Adding Team* as Ready for dev?") and sets it only after a yes. If
  design-validate isn't clean, say what's open before asking. After setting it,
  remind the designer to paste the container link into the Linear ticket.
- **Completed** is the engineer's call — same rule: ask, set only after a yes.
- Where the tool has no status (Paper, Pencil, code), status lives in Linear; ask
  before suggesting a change there.

---

## 2. Two modes: new work vs existing file

Decide which mode you're in before touching anything:
- **New work** — the user asked you to create screens or a flow, or you're adding new
  screens. Follow §2A: build it right first time, no questions about naming.
- **Existing file** — the screens/flows already exist and you didn't create them in
  this session (a link to an existing file, page or section; "tidy this up"; "get this
  ready for dev"). Follow §2B: **audit, then ask before changing anything.**

A mix (adding screens to an existing flow) uses §2A for the new screens and §2B for
anything that was already there.

## 2A. New work — build workflow

1. Locate or create the flow container. Ask for the flow name only if it can't be
   inferred.
2. Reuse the Feature word existing screens use; otherwise derive it from the flow.
3. Plan steps and states before drawing. Include the states design-validate would
   flag (§3.3) when in scope — at minimum Empty and Error for any list or form.
4. Name every screen at creation time. Never leave a default name.
5. Give every screen its note block (§1.3) for behaviour that isn't visible
   (scrolling, limits, validation, data source). If you add a tool annotation, add
   the same line to the block.
6. Self-check (§3.1) the container you touched, fix anything off, report its link.

Project design-system rules (tokens, layout, accessibility) still apply; this skill
adds the handoff structure on top.

## 2B. Existing file — audit, then ask

Never rename, move, regroup, rewrite notes or change status in an existing file
without an explicit yes. Read first, change nothing until the user answers.

### Step 1 — Audit (read-only)
Scan the page/section the user pointed at (adapter says how) and work out:
- **Flows**: which screens belong together. Clues: an existing section/frame, a
  floating title text above a row of screens, shared name prefixes, screens laid out
  in a row, prototype links between them.
- **Screens**: current name → proposed `Feature/NN Step/State` (step order from the
  layout left → right / prototype order; state from what's on screen: empty list,
  error message, loading spinner…).
- **Notes**: loose text near screens, notes not tagged `[NN Step]`, annotations not
  mirrored into a note block, screens with no notes.
- **Status**: current status of each flow container (Ready for dev / Completed / none).

### Step 2 — Show the summary
One short message, before any question:
```
I found 3 flows on "Explorations" (24 screens):
- "Team stuff" title + 9 screens → looks like Adding Team
- 6 screens named manage-inventory-1…6 → looks like Editing Team
- 9 screens not in any group
Names: 22 of 24 don't follow Feature/NN Step/State. Notes: 5 loose, 14 screens have none.
Status: none set.
```

### Step 3 — Ask, one topic at a time, in this order
How to ask depends on where you're running:
- **Claude Code / Claude desktop**: use the question tool (multiple choice, your
  proposal first) — one topic per question.
- **claude.ai, Codex, anything without a question tool**: a short numbered list in
  chat with your proposals; accept answers like "1 yes, 2 Teams, 3 skip".
Ask about one topic per message, with your proposal filled in so the user can just
say yes. Each question offers: **yes to all · let me pick · skip**. Wait for the
answer before the next topic.
1. **Flows** — "Wrap these 9 screens in a section called **Adding Team**?" (one
   question per flow; offer your proposed name and accept a different one).
2. **Feature word** — "Use **Team** as the feature name for both flows?"
3. **Screen names** — show the rename table (old → new) per flow, then "Rename these 9 screens?"
4. **Notes** — "Turn the 5 loose notes into note blocks under their screens and tag
   them? Add empty note blocks to the 14 screens with none?" Never rewrite the
   meaning of someone's note — only move, tag and type it.
5. **Status** — only after 1–4: "Mark **Adding Team** as Ready for dev?" If
   design-validate isn't clean, say what's open and offer to run it first.

If the user says "just do it all", still show the full list of changes once and get
one yes before applying.

### Step 4 — Apply and report
- Apply only what was approved, in small batches, flow by flow.
- Never delete anything. Replaced title text and empty wrapper frames are left in
  place (or moved aside) unless the user says to remove them.
- Report what changed with the container link(s), and what was skipped.

---

## 3. design-validate

Invoked through the separate [`design-validate`](../design-validate/SKILL.md) skill
(`/design-validate` in Claude, `$design-validate` in Codex), which runs this section.

```
/design-validate <container link> against <Linear ticket or PRD link | "no PRD"> [and <repo>]
```

### 3.1 Check names
- List the container's top-level screens (see adapter for how).
- Flag: wrong pattern, mixed Feature words, missing/duplicate NN, missing State,
  duplicate names, screens outside the container, loose title text, notes missing
  `[NN …]` or overlapping screens.
- Notes: screens with no note block, note lines whose tag matches no screen, and
  tool annotations not copied into the screen's block. Offer to copy them in (after yes).
- Propose a rename table (old → new). **Apply renames only after the user says yes**
  (screens the agent created this session are exempt).

### 3.2 Read the sources
- PRD / Linear ticket: requirements, roles, rules, limits.
- Repo (if given): data model — schemas, enums, relations, validation, uniqueness,
  status fields. Ground every decision in a concrete mismatch.
- "No PRD": proceed on screens + data model.
- Read notes and annotations as part of the design.

### 3.3 Look for gaps
- PRD requirements with no screen
- **Empty** and **lots of** (many items, pagination, scroll)
- **Long text** (names, emails, translations)
- **Invalid input** (validation, duplicates like "name already used")
- **Multi-membership** (users in several teams/roles)
- **Failure** (save fails, network, permission denied) and **Loading**
- **Destructive edges** (removing the last admin, deleting something in use)
- UI options that don't exist in the data model, or vice versa

### 3.4 Output — max 10 items, decisions first
```
<Flow>: <n> decisions, <m> missing states

Decisions (PM)
- <NN Step/State>: <question grounded in a PRD/data mismatch>

Missing states (design)
- <NN Step/State>: <state to draw>
```
One line each, every item anchored to a screen name. If clean, say so and give the
Ready-for-dev reminder. The designer fixes, sorts decisions with the PM, reruns.

---

## 4. Never
- Leave a created/edited screen unnamed or outside its flow container.
- Keep a screen's notes anywhere but on that screen (no separate notes files), or
  move a screen to another tool without its note block.
- Use a floating text title instead of a container.
- Rename, regroup, move notes or change status in an existing file without the user's yes (§2B).
- Set Ready for dev or Completed without asking first and getting a yes.
- Output more than 10 validate items, or put states above PM decisions.
- Hand back individual screen links when a container link exists.
- Put real file keys, tokens, internal URLs, PRD text or user data into this skill
  or its adapters — they are shared across the org.
