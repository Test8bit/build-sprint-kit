# Build Sprint Kit

A ready-to-use team of AI specialists for building your first personal dashboard with Claude Code.

This kit gives you five specialized AI roles — a team — that work together to build software for you. You hand them a one-page plan (a PRD), and they break it into phases and build it piece by piece.

It's the same kit used in the **Build Sprint** bootcamp.

## What's in the kit

Two folders, ten files:

```
.claude/
├── agents/                          ← The "WHO" — five subagent definitions
│   ├── orchestrator.md              ← Team lead
│   ├── backend-engineer.md          ← Server builder
│   ├── frontend-engineer.md         ← Page builder
│   ├── uiux-designer.md             ← Design system guardian
│   └── qa-engineer.md               ← Quality reviewer
└── skills/                          ← The "HOW" — five skill playbooks
    ├── orchestrator/SKILL.md
    ├── backend/SKILL.md
    ├── frontend/SKILL.md
    ├── uiux/SKILL.md
    └── qa/SKILL.md
```

**Subagents** are specialized Claude instances with focused roles. They run independently and don't bother each other.

**Skills** are detailed playbooks each subagent reads at the start of every task. They contain the standards, patterns, and rules each specialist follows.

**Why both?** This is the cutting-edge best practice for AI coding: combine portable expertise (skills) with role specialization (subagents). The team you get from this kit is what real product teams look like, with each role playing its part.

## The five roles

| Role | What they do |
|---|---|
| **Orchestrator** | Reads your plan, breaks it into phases, delegates to the right specialist, gates each phase with QA review. Always activates first. |
| **Back-end Engineer** | Writes Node + Express server code. Builds API endpoints that call the Anthropic API for interactive AI features. |
| **Front-end Engineer** | Writes `dashboard.html` and client-side JavaScript. Bakes live data from your connected apps (Gmail, Calendar, Notion, etc.) into the page. |
| **UI/UX Designer** | Owns the visual design system. Reviews and refines what the Front-end Engineer built. Keeps everything consistent. |
| **QA Engineer** | Reviews every phase against your plan. Catches bugs, missing edge cases, and PRD rule violations. Returns PASS, PASS WITH NOTES, or FAIL. |

## How to use it

See `INSTALL.md` for the step-by-step setup.

In short:
1. Clone this repo into a fresh project folder on your laptop
2. Make sure Claude Code is installed and you're logged into Claude Pro
3. Connect your services (Gmail, Calendar, Notion, ClickUp) in claude.ai → Settings → Connectors
4. Write your `CLAUDE.md` profile in the project root
5. Write a one-page PRD (or use the template if your bootcamp provides one)
6. Open Claude Code in the project folder and paste your PRD

The Orchestrator will activate first, plan the build, and walk you through it phase by phase.

## What you'll need

- **Claude Pro** ($20/month) — required for Claude Code
- **An Anthropic API account** — free $5 credit on signup, no payment method required
- **Claude Code installed** — `npm install -g @anthropic-ai/claude-code`
- **A laptop with admin rights and Node.js installed**
- **Connected services in claude.ai** — at minimum Gmail and Calendar. Add Notion, ClickUp, or any others your dashboard will use.

## License

MIT. Use it, modify it, share it. See `LICENSE` for details.

## Created for the Build Sprint bootcamp

This kit was built for the Build Sprint — a 2-day bootcamp teaching non-technical senior people how to build real AI-powered tools using Claude Code, Skills, Subagents, and MCP connectors.

If you're not in the bootcamp and you just found this useful — welcome. The kit works for any small-to-medium project where the team-of-roles framing makes sense.
