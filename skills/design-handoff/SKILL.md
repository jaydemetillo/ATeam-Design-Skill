---
name: design-handoff
description: Engineering x Design handoff conventions for any design or UI tool — Figma, Pencil (.pen), Paper, Storybook/code, or anything else Claude or Codex builds screens in. MANDATORY whenever the agent creates, edits, renames, organises, reads-for-build, or reviews screens. Enforces one container per flow, screen names in `Feature/NN Step/State`, notes tagged `[NN Step] …` under their screen, and a Ready-for-dev status the designer sets. Also runs design-validate — naming audit, PRD/data-model coverage, missing states (empty, many, long text, invalid input, multi-team users, failed saves) — max 10 items, PM decisions first. Triggers on any design-tool write, "validate this flow", "/design-validate", "is this ready for dev", "hand this off", "rename the screens", "organise this flow", or a design link plus a Linear ticket or PRD.
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

### 1.3 Notes
- Notes sit **directly under their screen**, inside the container, starting with
  the step tag: `[04 Review] This list can scroll, don't limit it to 3 people`.
- State-specific: `[04 Review/Choose admin] …`.
- A note must never overlap another screen. Tool-native annotations are fine if
  they don't block neighbouring screens; the agent reads them either way.

### 1.4 Sharing & status
- Share **one link to the container**, never a list of screen links. When reporting
  work, always give the container link.
- **Ready for dev** is the designer's call. The agent never sets it unprompted; when
  design-validate is clean it reminds the designer to mark it and paste the link
  into the Linear ticket.
- Engineers set **Completed** when shipped. The agent doesn't change it unless asked.

---

## 2. Build workflow

1. Locate or create the flow container. Ask for the flow name only if it can't be
   inferred.
2. Reuse the Feature word existing screens use; otherwise derive it from the flow.
3. Plan steps and states before drawing. Include the states design-validate would
   flag (§3.3) when in scope — at minimum Empty and Error for any list or form.
4. Name every screen at creation time. Never leave a default name.
5. Add `[NN Step]` notes for behaviour that isn't visible (scrolling, limits,
   validation, data source).
6. Self-check (§3.1) the container you touched, fix anything off, report its link.

Project design-system rules (tokens, layout, accessibility) still apply; this skill
adds the handoff structure on top.

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
- Use a floating text title instead of a container.
- Rename existing screens without the user's yes.
- Set Ready for dev or Completed unless explicitly told.
- Output more than 10 validate items, or put states above PM decisions.
- Hand back individual screen links when a container link exists.
- Put real file keys, tokens, internal URLs, PRD text or user data into this skill
  or its adapters — they are shared across the org.
