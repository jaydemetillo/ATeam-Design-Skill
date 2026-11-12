# Security

This repo holds Markdown skills that are loaded into AI tools (Claude, Codex, Figma,
Paper). Anything committed here may be read by those tools, so treat every file as
shareable.

## Never commit
- Secrets of any kind: API keys, tokens (Figma `figd_…`, GitHub, Linear), passwords, `.env` files
- Real Figma/Paper file keys, node links, or internal URLs — use placeholders like `<fileKey>`
- PRD text, Linear ticket contents, user data, or screenshots of real data

## What protects this repo
| Control | Where |
|---|---|
| Private repository, single owner | GitHub settings |
| Secret scanning on every PR and push (gitleaks, full history) | `.github/workflows/secret-scan.yml` |
| GitHub Actions linting | `.github/workflows/jactionlint.yml` |
| Actions pinned to commit SHAs, least-privilege `permissions` | all workflows |
| Dependabot alerts + security fixes; Actions updates with a 7-day cooldown | GitHub settings, `.github/dependabot.yml` |
| Code-owner review on skills and workflows | `.github/CODEOWNERS` |
| `.env*` and key files ignored | `.gitignore` |

## If a secret is committed
1. **Revoke/rotate it first** at the issuing service — removing it from git is not enough.
2. Remove it from the file and history (`git filter-repo`), then force-push.
3. Note what was exposed and when.

## Reporting
Report security issues privately to the repo owner (@jaydemetillo). Don't open a public issue.
