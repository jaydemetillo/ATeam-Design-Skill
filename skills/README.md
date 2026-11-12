# Shared agent skills

Skills here are the single source of truth for how AI agents (Claude, Codex, and
others) work with our designs. Edit them here, by pull request; every tool reads
the same files.

| Skill | What it does |
|---|---|
| [`design-validate`](design-validate/SKILL.md) | `/design-validate <section> against <PRD \| "no PRD"> [and <repo>]` — runs the check from design-handoff §3. Install alongside design-handoff. |
| [`design-handoff`](design-handoff/SKILL.md) | Engineering x Design conventions: one container per flow, `Feature/NN Step/State` screen names, `[NN Step]` notes, Ready for dev, and the `design-validate` check. Adapters for Figma, Pencil, Paper and code. |

## Install

### Claude Code (CLI / desktop)
Use a symlink so `git pull` keeps you current:
```bash
ln -s "$(pwd)/skills/design-handoff" ~/.claude/skills/design-handoff
```
Inside this repo it also works as a project skill — add the same symlink under `.claude/skills/`.

### claude.ai (Team plan)
An org admin uploads the skill once and publishes it to the team:
1. Zip the folder: `cd skills && zip -r design-handoff.zip design-handoff`
2. claude.ai → Settings → Capabilities → Skills → upload → share with the organisation.
3. Re-upload after each merged change to `skills/design-handoff/`.

### Codex
Works in this repo out of the box (`.agents/skills/design-handoff` + `AGENTS.md`).
For use everywhere, see [`design-handoff/adapters/codex.md`](design-handoff/adapters/codex.md).

### Paper
Needs Paper Desktop's local MCP server connected to your agent — see
[`design-handoff/adapters/paper.md`](design-handoff/adapters/paper.md).

### Other tools
Paste the rules (§1 of `SKILL.md`) plus the matching adapter into the tool's custom
instructions.

## Rules for contributing
- Changes go through a pull request reviewed by a code owner (see `.github/CODEOWNERS`).
- Never commit secrets, tokens, real Figma file keys, internal links, PRD text or
  user data into a skill — skills are shared across the organisation and loaded into
  third-party AI tools. Use placeholders like `<fileKey>`.
- Skills are Markdown only. Adding a script means it gets the same review as app code.
