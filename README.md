# LinkedIn Recon

AI-powered hiring network mapping. Give it a job posting, get back the real decision-maker, their org context, and a plan to reach them.

## What it does

1. **Reverse-lookup** the hiring manager from a job posting
2. **Map the org structure** around them (reports-to, peers, team)
3. **Deep-analyze** each target (posts, resume, public talks, comment network)
4. **Output** an interactive relationship graph + personalized outreach drafts

Built on Claude (Anthropic) + LinkedIn API (via Apify) + D3.js.

## Install

### Claude Code

```bash
cp recon.md ~/.claude/commands/recon.md
```

Then run `/recon` in any Claude Code session.

### Codex

Copy `recon.md` to your project's agent instructions directory and reference it in your `AGENTS.md`.

## Prerequisites

- **Apify account** (free tier works, $5/month credit) — the skill will guide you through setup on first run
- **Claude Code** or **Codex** environment

## Files

| File | Purpose |
|---|---|
| `recon.md` | The skill file (install this) |
| `linkedin-network-mapping-sop.md` | Top-level 3-step workflow |
| `relationship-mapping-sop.md` | Detailed 5-layer methodology |
| `process-constraints.md` | File naming, progressive disclosure rules |

## Cost

< $0.05 per investigation (LinkedIn API calls). Full run takes ~30 minutes.

## License

MIT
