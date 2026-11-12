# ATeam Design Skill

Shared AI-agent skills for how **design hands off to engineering** at Open Government
Products. One set of rules, used by Claude, Codex and any design tool they drive —
Figma, Paper, Pencil, or code/Storybook.

> **TL;DR** — each flow lives in its own container, screens are named
> `Feature/NN Step/State`, notes are tagged `[NN Step]`, the agent runs
> `design-validate`, the designer marks **Ready for dev**, engineers build from one
> link and mark **Completed**.

## What's in here

```
skills/
  README.md                      ← install + contributing rules
  design-handoff/
    SKILL.md                     ← the rules + design-validate (tool-agnostic)
    adapters/
      figma.md                   ← sections, frame names, Dev Mode
      paper.md                   ← Paper Desktop MCP, artboards
      pencil.md                  ← .pen files via pencil MCP
      code.md                    ← Storybook story titles
      codex.md                   ← running the skill from Codex
.claude/skills/design-handoff  →  skills/design-handoff   (Claude Code, auto-loaded here)
.agents/skills/design-handoff  →  skills/design-handoff   (Codex, auto-loaded here)
AGENTS.md / CLAUDE.md            ← tell every agent to use the skill
docs/starter-kit.md              ← docs for the OGP Starter Kit app scaffold in apps/, packages/
```

## Quick start

| You use… | Do this |
|---|---|
| **Claude Code** | Work inside this repo — it loads automatically. Everywhere else: `ln -s "$(pwd)/skills/design-handoff" ~/.claude/skills/design-handoff` |
| **claude.ai** (Team plan) | An org admin uploads `skills/design-handoff` as a zip under Settings → Capabilities → Skills and shares it with the org |
| **Codex** | Works inside this repo. Everywhere else: see [`adapters/codex.md`](skills/design-handoff/adapters/codex.md) |
| **Paper** | Connect Paper Desktop's MCP to your agent: see [`adapters/paper.md`](skills/design-handoff/adapters/paper.md) |
| **Figma** | Connect the Figma MCP to your agent: see [`adapters/figma.md`](skills/design-handoff/adapters/figma.md) |

Then ask your agent:

```
/design-validate <section link> against <Linear ticket or PRD link | "no PRD"> [and <repo>]
```
(Codex: `$design-handoff validate …`)

It checks screen names (and offers to rename — only after you say yes), reads the
PRD and data model, and returns at most 10 items: **PM decisions first, then missing
states to draw.** Fix, rerun until clean, then mark the section Ready for dev and paste
its link into Linear.

## The rules in one screen

| | Rule |
|---|---|
| **Flow** | One container per flow (Figma section, Paper/Pencil frame, Storybook group), named after the flow |
| **Screens** | `Feature/NN Step/State` — e.g. `Team/03 Add users/One selected`. Mobile: add `/Mobile` |
| **Notes** | Text under the screen: `[04 Review] This list can scroll…` or `[04 Review/Choose admin] …` |
| **Share** | One link to the container, never a list of screen links |
| **Status** | Designer marks Ready for dev; engineer marks Completed. Agents never set either |

Full detail: [`skills/design-handoff/SKILL.md`](skills/design-handoff/SKILL.md).

## Contributing

- Change skills by pull request; `.github/CODEOWNERS` requires a design-owner review on `skills/`.
- Skills are Markdown only. **Never** commit secrets, tokens, real Figma file keys,
  internal URLs, PRD text or user data — skills are loaded into third-party AI tools.
  Use placeholders like `<fileKey>`.
- Adding a tool? Copy an adapter, map each concept in SKILL.md §1, and add a row to
  the table in SKILL.md "Step 0".

## Starter Kit scaffold

This repo was created from the OGP Starter Kit; the app scaffold (`apps/`, `packages/`,
`tooling/`) and its CI are unchanged. See [`docs/starter-kit.md`](docs/starter-kit.md).
