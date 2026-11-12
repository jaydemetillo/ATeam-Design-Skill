# Agent instructions

This repo is the source of truth for the **design-handoff** skill.

## Use the skill
Before creating, editing, renaming, reviewing or building from screens — in Figma,
Paper, Pencil, Storybook or code — read
[`skills/design-handoff/SKILL.md`](skills/design-handoff/SKILL.md) and the adapter for
your tool, and follow them. In short: one container per flow, screens named
`Feature/NN Step/State`, notes tagged `[NN Step]`, never set Ready for dev yourself.
Run its `design-validate` check when asked whether a flow is ready for dev.

## Editing this repo
- Skills are Markdown only. Keep `SKILL.md` tool-agnostic; tool specifics go in `adapters/`.
- Never write secrets, tokens, real file keys, internal URLs, PRD text or user data
  into any file — see [`SECURITY.md`](SECURITY.md). Use placeholders like `<fileKey>`.
- Pin any GitHub Action to a full commit SHA with a version comment, and give each job
  the least `permissions` it needs.
