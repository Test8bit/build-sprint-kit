---
name: frontend
description: "Front-end engineering playbook. Build dashboard.html with MCP data baked in at generation time, plus client-side JS for interactive AI features."
---

# Front-end Engineer Skill

You build `dashboard.html` and the client-side JavaScript inside it. Your work is what the user sees and interacts with. The data display (calendar, inbox, tasks, notion) gets baked in by you at generation time using MCPs. The interactive buttons (Generate Brief, Generate Reply, Regenerate, Suggest, Ask) call the backend's API endpoints.

## The architecture you build for

**Hybrid: static page generated from MCP + interactive buttons that hit the local server.**

- One file: `dashboard.html`. Contains the structure, the styling (or links to a CSS file), the data baked inline, and the JavaScript for interactive buttons.
- When you build a data feature (calendar, inbox, tasks, notion), you fetch live data from the appropriate MCP, then write it into the HTML.
- When you build an interactive feature (Generate Brief, Generate Reply, Regenerate, Suggest, Ask), you wire a button that calls `http://localhost:3001/api/...` via `fetch()`.
- The data is a snapshot. To refresh, the user re-runs the build prompt.

## Phase 1 — Building the skeleton

When the Orchestrator delegates Phase 1, build a single file: `dashboard.html`. Structure top-to-bottom:

### Header

- A small uppercase date label at the very top — formatted like `FRIDAY, MAY 8, 2026`. Compute this in JavaScript from the current date. Use the full uppercase day name, comma, full uppercase month name, day number with no leading zero, comma, four-digit year.
- A large bold greeting below it on its own line, ending with a period — e.g., `Good afternoon, [name].`
- Time-based greeting (computed in JS at page load):
  - Before 12:00 PM local time → `Good morning`
  - 12:00 PM to 5:59 PM → `Good afternoon`
  - 6:00 PM onward → `Good evening`
- The name comes from `CLAUDE.md` — read the file at generation time, extract a clean name from the WHO section, and write it into the HTML as a string. If you can't parse a name, fall back to `there` (e.g., `Good afternoon, there.`).

### Search bar

- A wide search bar below the header, roughly 50–60% of page width, left-aligned
- Placeholder text: `Ask anything about your documents, schedule, or tasks...`
- A subtle keyboard shortcut indicator on the right: `⌘ K`
- Visually present but NOT functional yet — wired in the Ask bar phase

### Four cards in a row

- Equal width, generous horizontal gap between them
- Each card: white background, 1px subtle light gray border, ~12px border radius, **no shadows**, generous internal padding
- Each card is **tall** — at least 500px in height, even when empty
- Top-left inside each card: small uppercase label with letter-spacing, light gray — `TODAY`, `INBOX`, `TASKS`, `KNOWLEDGE`
- Vertical and horizontal center of each card: light gray text `No data yet` (placeholder, replaced in feature phases)

### Style

- Background color: `#F5F4EE` (warm cream)
- Typography: system font stack — `system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif`
- Generous whitespace everywhere
- No images, no icons, no SVGs (Apple-minimalist)
- All inline in `<style>` tag (single-file simplicity)

### Rules for Phase 1

- No data fetching. The cards show `No data yet` hardcoded.
- The search bar is not functional yet.
- The cards are not clickable yet.
- Greeting and date update on page load.

## Phase 2 — Calendar card (TODAY)

When the Orchestrator delegates the Calendar phase:

### Step 1 — Fetch the data

Use the Calendar MCP (connected via Claude.ai → Connectors). Fetch today's events:
- Time range: start of today (local time) through end of today
- Order by start time
- Include attendees, title, start time, end time, location

### Step 2 — Bake the data into the HTML

Inside the TODAY card, replace `No data yet` with a list of events. For each event:
- Time on the left (e.g., `9:00 AM`)
- Title in the middle
- Duration or location below the title in lighter gray
- A small color bar on the left edge:
  - Red: urgent or external partner (any attendee with a non-personal external email domain — e.g., not gmail.com or yahoo.com)
  - Blue: standard internal meeting (multiple attendees, same email domain)
  - Green: 1:1 or small team (2–3 attendees)
  - Gray: solo focus block (no attendees)

Solo blocks are NOT clickable. Real meetings are clickable.

### Step 3 — Wire the click interaction

When a meeting is clicked, open a modal showing the **basics first** — no AI call yet:
- Meeting title at top
- Date (formatted full, e.g., `Thursday, May 8, 2026`)
- Start and end time
- Location (if any)
- "Who's in the room" section: each attendee's name + inferred company from email domain (e.g., `@globe.com → Globe Telecom team member`)

Below the basics, a **Generate Brief** button. When clicked:

```javascript
async function generateBrief(meetingData) {
  // show loading state
  const response = await fetch('http://localhost:3001/api/meeting/brief', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ meeting: meetingData }),
  });
  const data = await response.json();
  // render data.companyContext + data.suggestedQuestions in the modal
  // replace Generate Brief button with Copy Brief button
}
```

After the brief renders, replace the Generate Brief button with a **Copy Brief** button that copies the full brief text to the clipboard.

### Edge cases

- No events today: card shows `No meetings today. You have the day.`
- Meeting has no attendees: Generate Brief button is disabled.
- Anthropic call fails: show `Brief unavailable — try again in a moment.` inline. Keep Generate Brief button visible.

## Phase 3 — Email card (INBOX)

### Step 1 — Fetch the data

Use the Gmail MCP. Fetch:
- Latest 10 unread emails from the past 7 days
- Skip senders matching: newsletter, no-reply, notifications, marketing
- For each email: sender name + email, subject, time received, full body

### Step 2 — Bake the data into the HTML

Inside the INBOX card, list the emails. For each:
- Sender name on top
- Subject below
- Time received in small light gray
- A one-line preview of the body
- A small blue dot indicating unread

Empty state: `Inbox clear. Nothing to triage.`

### Step 3 — Wire the click interaction

When an email is clicked, open a modal showing the **email itself first** — no AI call yet:
- Subject at top
- Sender name and email
- Time received
- Full email body (preserve line breaks)

Below the email, a **Generate Reply** button. When clicked:

```javascript
async function generateReply(emailData) {
  // show loading state
  const response = await fetch('http://localhost:3001/api/email/reply', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ email: emailData }),
  });
  const data = await response.json();
  // render data.draftReply in an editable textarea
  // if data.sourceFiles is non-empty, show "Grounded in: [filenames]" footer
  // replace Generate Reply button with Regenerate / Save Draft & Open Gmail / Cancel
}
```

The Regenerate button calls the same endpoint again and replaces the draft text. Save Draft & Open Gmail uses the Gmail MCP at click time (call Claude Code via a separate endpoint or instruct the user) — for now, mark this as `[backend handles this]` and let the backend-engineer figure out the path.

### Edge cases

- No unread emails: card shows `Inbox clear. Nothing to triage.`
- AI call fails: show inline error, keep previous draft visible if there was one.

## Phase 4 — Tasks card (TASKS)

### Step 1 — Fetch the data

Use the ClickUp MCP. Fetch the user's open tasks. Display:
- Task title
- Due date if any
- Priority indicator if set
- Group by list or sprint if it makes the card more readable

### Step 2 — Bake the data into the HTML

Inside the TASKS card, list the tasks with checkboxes on the left (visual only — clicking just toggles strikethrough locally). At the top of the card, add a small **Suggest** button next to a **+** button.

The + button adds a blank task input inline (local-only, not synced to ClickUp).

The **Suggest** button, when clicked:

```javascript
async function suggestTasks() {
  // gather context from the page: today's calendar events, today's emails (both already baked in)
  const calendarEvents = window.__calendarData;
  const emails = window.__inboxData;

  const response = await fetch('http://localhost:3001/api/tasks/suggest', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ calendar: calendarEvents, emails: emails }),
  });
  const data = await response.json();
  // render data.suggestions in a chooser modal — each with a checkbox, unchecked by default
  // "Add selected" button adds checked items to the task list locally
  // No labels on added tasks (no "suggested" / "email" / "calendar" tags)
}
```

When baking data into the page, also expose it on the window object (`window.__calendarData`, `window.__inboxData`) so the interactive features can access it without re-fetching.

### Edge cases

- No open ClickUp tasks: show `Nothing open. Either you're caught up or it's time to plan.`
- Suggest returns nothing: chooser modal shows `Nothing actionable detected right now.`

## Phase 5 — Knowledge card (KNOWLEDGE)

### Step 1 — Fetch the data

Use the Notion MCP. Fetch the user's most recently edited pages from the past 14 days. Max 8 pages. For each:
- Page title
- Parent database or workspace location
- Time of last edit

### Step 2 — Bake the data

List in the KNOWLEDGE card. Show title + parent + relative time (e.g., `2 hours ago`).

Empty state: `Nothing recent in Notion.`

No interactive AI buttons on this card for the bootcamp. Just display.

## Phase 6 — Ask bar

Wire the search bar at the top.

### Behavior

- Press **⌘K** (Mac) or **Ctrl+K** (Windows) from anywhere on the page focuses the search bar.
- Type a question, press Enter. The dashboard reads the current data already on the page (calendar, inbox, tasks, notion — all baked in or in localStorage) and POSTs to `/api/ask`:

```javascript
async function askQuestion(question) {
  const response = await fetch('http://localhost:3001/api/ask', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
      question,
      calendar: window.__calendarData,
      emails: window.__inboxData,
      tasks: getTasksFromPage(),
      notion: window.__notionData,
    }),
  });
  const data = await response.json();
  // render data.answer in a panel below the search bar
  // show source citations as small badges
}
```

- Press Escape to close the answer panel.
- Show a loading state while waiting.

## Rules for everything you build

1. **Read CLAUDE.md at generation time.** Use it to personalize the greeting, infer tone, etc. Don't embed CLAUDE.md in the HTML — keep it server-side.

2. **MCP fetches happen at generation time only.** Once `dashboard.html` is written, no runtime MCP calls. The data is a snapshot.

3. **All AI calls go through the local backend.** The dashboard's JS hits `http://localhost:3001/api/...`. It never calls the Anthropic API directly.

4. **Never invent data.** If an MCP returns nothing for a card, show the empty state — don't generate fake events or emails.

5. **Cite sources for any AI-generated text** that gets displayed. The Ask bar's answer shows source badges. The meeting brief shows attendees pulled from real calendar data.

6. **Keep it single-file when possible.** `dashboard.html` should be self-contained — inline `<style>`, inline `<script>`. Easier to share, easier to debug.

7. **Use the design system enforced by uiux-designer.** Don't make up colors or spacing. If you're not sure, ask the Orchestrator to bring in @uiux-designer.

8. **Accessibility basics:** buttons are real `<button>` elements (not divs). Modals trap focus. Keyboard shortcuts have visible indicators.

## What to do if a task is outside your scope

If the Orchestrator delegates server work, OAuth setup, or pure visual design decisions to you, stop and respond:

```
This task is outside my scope. It looks like work for @[correct-specialist]. Please re-delegate.
```

## Quality bar before handing back

- [ ] The page renders cleanly in the browser
- [ ] All baked-in data is real (from MCP) — no placeholder text remaining
- [ ] Interactive buttons hit the right backend endpoint
- [ ] Greeting and date update correctly on page load
- [ ] Empty states are handled
- [ ] No console errors on page load
- [ ] CLAUDE.md was read at generation time and reflected in the output (greeting at minimum)

If any check fails, fix it before handing back.
