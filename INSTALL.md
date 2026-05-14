# Installation Guide

This guide walks you through getting the Build Sprint Kit installed on your laptop and ready to use with Claude Code.

**Time estimate:** 10–15 minutes if you already have Claude Pro and your services connected. 30+ minutes if you're starting from scratch.

---

## Step 1 — Check prerequisites

Before cloning the kit, make sure you have:

- **A laptop with admin rights** (Mac or Windows)
- **Node.js installed** — download from [nodejs.org](https://nodejs.org). Pick the LTS version. Run the installer with defaults.
- **VS Code installed** — download from [code.visualstudio.com](https://code.visualstudio.com)
- **Git installed** — [git-scm.com](https://git-scm.com). On Mac it might already be there; type `git --version` in Terminal to check.
- **Claude Pro account** — sign up at [claude.ai](https://claude.ai). You need the $20/month plan, not free.
- **Claude Code installed** — open a terminal and run:
  ```
  npm install -g @anthropic-ai/claude-code
  ```
  Then verify:
  ```
  claude --version
  ```
  If you see a version number, it's installed.

- **Anthropic API account** — sign up at [console.anthropic.com](https://console.anthropic.com). You'll get $5 in free credits automatically; no payment method needed. Don't generate the API key yet — we'll do that in Step 4.

---

## Step 2 — Connect your services in Claude

In your browser:

1. Go to [claude.ai](https://claude.ai)
2. Click your profile picture (bottom left)
3. Settings → **Connectors**
4. Connect at minimum: **Gmail**, **Google Calendar**
5. Recommended additions for the full dashboard experience: **Notion**, **ClickUp**

Each connector is a one-click setup. Click Connect → sign in with your account for that service → done.

You only need to do this once. Claude Code on your laptop will inherit these connections automatically.

---

## Step 3 — Clone the kit into a fresh project folder

Open your terminal. Pick where you want the project to live (Desktop is fine).

```bash
cd ~/Desktop
git clone https://github.com/eden-ventures/build-sprint-kit.git my-dashboard
cd my-dashboard
```

This creates a new folder called `my-dashboard` with the kit inside.

Verify the kit installed correctly:

```bash
ls -la .claude
```

You should see two folders: `agents` and `skills`. If you don't see them, the clone didn't work — re-check the URL and try again.

---

## Step 4 — Generate your Anthropic API key and save it

Back in your browser:

1. Go to [console.anthropic.com](https://console.anthropic.com)
2. **Settings → API Keys**
3. Click **Create Key**
4. Name it something memorable (e.g., `my-dashboard-bootcamp`)
5. **Copy the key immediately** — it starts with `sk-ant-api03-...`. You'll only see it once.

Now save it to your project. In the `my-dashboard` folder:

1. Create a new file called `.env` (the dot at the start is important)
2. Open it in VS Code
3. Type exactly:
   ```
   ANTHROPIC_API_KEY=sk-ant-api03-...
   ```
   Replace `sk-ant-api03-...` with the real key you just copied. No spaces around the `=`. No quotes.
4. Save the file.

**The `.env` file is already in `.gitignore`**, so your key will never accidentally get pushed to GitHub.

---

## Step 5 — Write your `CLAUDE.md` profile

Your `CLAUDE.md` file is your personal profile. Every AI action reads it first, so the dashboard ends up sounding like you.

In the `my-dashboard` folder, create a file called `CLAUDE.md` with these six sections:

```markdown
# WHO I am
[Your role, company, identity in one short paragraph]

# WHAT I do
[Your focus areas, ongoing work, what fills your weeks]

# WHERE I work
[The people and contexts around you — team, key collaborators]

# WHEN I work
[Your cadence — workdays, hours, weekly rhythms]

# HOW I want you to behave
[Your tone, voice, formatting preferences]

# Out of scope
[The refusal rule — what the tool must NEVER answer]
```

Keep it short — 1–3 sentences per section is enough. You can iterate later.

**The most important section is "Out of scope."** This is the rule that makes your dashboard refuse general questions and stay focused on your work.

---

## Step 6 — Open Claude Code and verify the team loaded

In your terminal, still in the `my-dashboard` folder:

```bash
claude
```

Claude Code launches in your terminal. Try this prompt:

```
What subagents and skills do you have available in this project?
```

You should see all five subagents listed (orchestrator, backend-engineer, frontend-engineer, uiux-designer, qa-engineer) and all five skills (orchestrator, backend, frontend, uiux, qa).

If you see them — **the team is assembled and ready.**

If you see an error like "no closing --- found" or "skills not loading" — close Claude Code, check the files in `.claude/skills/` and `.claude/agents/` haven't been corrupted, and try again.

---

## Step 7 — You're ready

You now have:

- ✅ All five team members loaded into Claude Code
- ✅ Your services connected (Gmail, Calendar, etc.)
- ✅ Your Anthropic API key saved in `.env`
- ✅ Your `CLAUDE.md` profile written

The next step is writing your one-page PRD and pasting it into Claude Code. The Orchestrator will activate first and walk you through the build phase by phase.

If you're in the Build Sprint bootcamp, your facilitator will hand you the PRD format on Day 2. If you're doing this on your own, look at the `prd-template.md` in this repo (coming soon) for the structure.

---

## Troubleshooting

**Claude Code says "no closing --- found"** — a skill file's YAML frontmatter is corrupted. Re-clone the repo into a fresh folder.

**Claude Code can't see the skills or subagents** — make sure you opened Claude Code from inside the `my-dashboard` folder. Skills load from `.claude/skills/` relative to where you start Claude Code.

**`git clone` fails with authentication error** — make sure the repo URL is correct. This is a public repo so no authentication should be needed.

**`npm install -g` fails with permission errors** — on Mac, prefix with `sudo`. On Windows, run the terminal as administrator.

**Connectors show but Claude Code can't see Gmail/Calendar data** — make sure you connected the services in the same Anthropic account that Claude Code is logged into. They share login, but you can check by going to claude.ai → Settings → Connectors and confirming the status shows "Connected" (not "Disconnected" or "Error").

**Anthropic API key not working** — double-check the `.env` file has no spaces, no quotes, and the key starts with `sk-ant-api03-`. The line should be exactly:
```
ANTHROPIC_API_KEY=sk-ant-api03-...
```

---

If something goes wrong that's not on this list, ask your bootcamp facilitator. Or open an issue on this repo with the exact error message.
