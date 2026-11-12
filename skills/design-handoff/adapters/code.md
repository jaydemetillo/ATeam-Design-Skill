# Adapter — Code (Storybook, React screens)

Use when screens are built in code (Claude Code, Codex, Cursor) rather than drawn.

| Concept | In code |
|---|---|
| Flow container | A Storybook title group — every story for the flow shares the prefix `Flows/<Flow>` |
| Screen | One story per screen state |
| Screen name | Story title + name: `title: 'Flows/Adding Team/Team/03 Add users'`, export `OneSelected` with `name: 'One selected'` |
| Note | Story `parameters.docs.description.story`, starting `[03 Add users] …` |
| Container link | Storybook URL for the `Flows/<Flow>` group (Chromatic link once published) |
| Ready for dev / Completed | PR label or Linear status — not in code |

## Mechanics
- Storybook splits titles on `/`, so the sidebar groups flow → feature → step and the
  states sort under each step. Keep the two-digit `NN` so steps sort in order.
- One story per state in §3.3 that applies (Empty, Error, Loading, Long text…) — these
  double as the visual test cases.
- If the project has a Storybook MCP, use its docs tools before using any component
  prop, and run its story tests after adding stories. Follow the project's own `AGENTS.md`.
- **Read names**: list stories under `Flows/<Flow>` (Storybook MCP or by grepping
  `title:` in `*.stories.tsx`).
- Use fixture data only in stories — never real user records.
