# Adapter — Codex (OpenAI Codex CLI / IDE / app)

Codex is an agent, not a design tool: it runs this skill and then uses the adapter for
whatever it's working in (Figma, Paper, Pencil, or code). This file covers setup and
the Codex-specific differences.

## Setup
Codex supports the same `SKILL.md` format (Agent Skills standard).

- **In this repo**: nothing to do. `.agents/skills/design-handoff` links to
  `skills/design-handoff`, and `AGENTS.md` points Codex at it.
- **Everywhere else** (personal):
  ```bash
  mkdir -p ~/.agents/skills
  ln -s "<path-to-repo>/skills/design-handoff" ~/.agents/skills/design-handoff
  ```
  Older Codex versions read `~/.codex/skills/` instead — link there too if the skill
  doesn't show up.
- Invoke explicitly with `$design-handoff`, or let Codex pick it from the description.

## Design tools from Codex
Add MCP servers in `~/.codex/config.toml`:

| Tool | How |
|---|---|
| Figma | Figma's MCP server (remote, or Figma Desktop's local server) — see [figma.md](figma.md) |
| Paper | Paper Desktop's local server `http://127.0.0.1:29979/mcp` — see [paper.md](paper.md) |
| Pencil | Pencil MCP — see [pencil.md](pencil.md) |
| Code / Storybook | No MCP needed for files; Storybook MCP per `AGENTS.md` — see [code.md](code.md) |

## Differences from Claude
- `design-validate` is its own skill: `$design-validate <container link> against <PRD | "no PRD">`.
  Link `skills/design-validate` alongside `skills/design-handoff` when installing outside this repo.
- Codex has no per-tool approval prompt for every MCP write in some sandbox modes —
  the "rename only after the user says yes" rule still applies: print the rename
  table and wait for an explicit yes in chat before writing.
- Keep MCP auth tokens in Codex's config or keychain, never in this repo.
