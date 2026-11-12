# Adapter — Code (Storybook, React screens)

Use when screens are built in code (Claude Code, Codex, Cursor) rather than drawn.

| Concept | In code |
|---|---|
| Flow container | A Storybook title group — every story for the flow shares the prefix `Flows/<Flow>` |
| Screen | One story per screen state |
| Screen name | Story title + name: `title: 'Flows/Adding Team/Team/03 Add users'`, export `OneSelected` with `name: 'One selected'` |
| Note | The screen's note block in its story docs (or a comment atop the screen component) |
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

## Notes on each screen
In code, the screen is its story (or its screen component), so the note block lives there.
- **Storybook**: in the screen's story, verbatim, one line per fact:
  ```ts
  export const OneSelected: Story = {
    name: 'One selected',
    parameters: {
      docs: {
        description: {
          story: [
            '[03 Add users/One selected] Interaction · Add button: enabled once one user is picked',
            '[03 Add users] Data · User list: only active members of this agency',
          ].join('\n\n'),
        },
      },
    },
  }
  ```
- **No Storybook**: a comment block at the top of the screen's component, same lines:
  ```ts
  /**
   * Handoff notes — Team/03 Add users/One selected
   * [03 Add users/One selected] Interaction · Add button: enabled once one user is picked
   */
  ```
- Engineers add `Built as…` / `Changed because…` lines in the same place, tagged the
  same way, so the next design pass can read them back.
- Never move notes to a shared NOTES.md or wiki — they stay on the screen.
