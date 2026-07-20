# Claude Code — Student Handbook
### Building Splitmate — An Expense Sharing App

> **Project:** Splitmate — a multi-user expense sharing app. You will register, log in, join multiple groups, add expenses, and see balances across all groups.
> **Tech Stack:** React + JavaScript + Tailwind CSS + LocalStorage (Phase 1) → Supabase Auth + PostgreSQL (Phase 2 via MCP)

---

## Table of Contents

1. [How Claude Code Works](#1-how-claude-code-works)
2. [Setup & Installation](#2-setup--installation)
3. [Start Project / Ask Questions / Make Code Changes](#3-start-project--ask-questions--make-code-changes)
4. [Plan Mode](#4-plan-mode)
5. [Slash Commands](#5-slash-commands)
6. [Session & Conversation Management](#6-session--conversation-management)
7. [Models & Usage](#7-models--usage)
8. [When Claude Gets It Wrong](#8-when-claude-gets-it-wrong)
9. [CLAUDE.md](#9-claudemd)
10. [Tools](#10-tools)
11. [Bash Mode](#11-bash-mode)
12. [Permissions & Allowed Tools](#12-permissions--allowed-tools)
13. [Context — Add Files / Images / Assets](#13-context--add-files--images--assets)
14. [Context Window & Management](#14-context-window--management)
15. [Hooks](#15-hooks)
16. [MCP (Model Context Protocol)](#16-mcp-model-context-protocol)
17. [Skills](#17-skills)
18. [Custom Slash Commands](#18-custom-slash-commands)
19. [Plugins](#19-plugins)
20. [Understanding Agents](#20-understanding-agents)
21. [Subagents](#21-subagents)
22. [Agent Teams](#22-agent-teams)
23. [Custom Subagent Files](#23-custom-subagent-files)
24. [Agent Teams in Practice](#24-agent-teams-in-practice)
25. [Spec-Driven Development](#25-spec-driven-development)
26. [Claude Code: VS Code Extension](#26-claude-code-vs-code-extension)
27. [GitHub CLI](#27-github-cli)
28. [Claude Code Desktop](#28-claude-code-desktop)
29. [Claude Code Web](#29-claude-code-web)

---

## 1. How Claude Code Works

📖 **Docs:** https://code.claude.com/docs/en/overview

> **What you'll learn:** Understand what Claude Code is and how it is different from a regular AI chat.
>
> **What you'll build:** No project work yet — this is the conceptual foundation before you write a single line.


### What It Is
Claude Code is an **agentic AI coding assistant** that lives in your terminal. Unlike a chat interface where you copy-paste code, Claude Code reads, writes, and executes files directly inside your project — it operates inside your codebase, not outside it.

### The Agentic Loop
Claude Code works in a continuous loop:

```
Your Prompt
 ↓
Claude thinks (reasons about the task)
 ↓
Claude picks a Tool (Read File / Write File / Run Bash / Search...)
 ↓
Tool executes → Result returned to Claude
 ↓
Claude thinks again → picks next tool or responds to you
 ↓
Final response / code written to your files
```

This loop continues until the task is done or Claude needs your input.

### What Makes It Different from AI Chat

| Regular AI Chat | Claude Code |
|---|---|
| You copy-paste code manually | Claude writes directly to your files |
| No terminal access | Runs `npm install`, tests, git commands, etc. |
| Forgets everything between sessions | `CLAUDE.md` provides persistent memory |
| One response per prompt | Multiple tool calls per task |
| Describes what to do | Actually does it |

### Key Concepts at a Glance

- **Tools** — The actions Claude can take (read, write, bash, search)
- **Context Window** — How much Claude can "see" at once (like RAM)
- **CLAUDE.md** — A file Claude always reads — your project's memory
- **Permissions** — You control exactly what Claude is allowed to do
- **Hooks** — Scripts that auto-run before/after Claude's actions
- **MCP** — Connects Claude to external services like Supabase

### In Our Project — Splitmate
When you say *"Set up our Splitmate expense sharing app"*, Claude doesn't just tell you what commands to run — it actually creates the folder structure, writes the initial files, installs dependencies, and sets up the project. You watch it happen step by step in your terminal.

---

## 2. Setup & Installation

📖 **Docs:** https://code.claude.com/docs/en/setup

> **What you'll learn:** Install Claude Code, authenticate, and get a session running for the first time.
>
> **What you'll build:** Splitmate project created — Vite + React + Tailwind scaffolded, dev server running, all routes wired.


### System Requirements
- **OS:** macOS 13+, Windows 10/11, Ubuntu 20.04+, Debian 10+
- **RAM:** 4 GB minimum
- **Internet:** Required (Claude calls Anthropic's servers)
- **Account:** Claude Pro ($20/mo) or higher — free plan does not include Claude Code

### Install Claude Code — Recommended: Native Installer

Anthropic's native installer is the **recommended method** as of 2026. It requires **no Node.js**, installs a single binary, and **auto-updates in the background**.

---

**macOS:**
```bash
# Open Terminal and run:
curl -fsSL https://claude.ai/install.sh | bash
```

After it completes, open a **new terminal window** and verify:
```bash
claude --version
```

*Alternative — Homebrew (does not auto-update):*
```bash
brew install --cask claude-code
# To update later: brew upgrade claude-code
```

---

**Windows:**

First install **Git for Windows** from [gitforwindows.org](https://gitforwindows.org) if you don't have it — Claude Code requires it for native Windows.

Then open **PowerShell** and run:
```powershell
irm https://claude.ai/install.ps1 | iex
```

Or from **CMD**:
```cmd
curl -fsSL https://claude.ai/install.cmd -o install.cmd && install.cmd && del install.cmd
```

> **Tip:** Your prompt shows `PS C:\` in PowerShell and `C:\` in CMD. Use the right command for your shell.

*Alternative — WinGet (does not auto-update):*
```powershell
winget install Anthropic.ClaudeCode
# To update later: winget upgrade Anthropic.ClaudeCode
```

*Alternative — WSL (Windows Subsystem for Linux):*
```bash
# Inside your WSL terminal (Ubuntu/Debian):
curl -fsSL https://claude.ai/install.sh | bash
```
WSL gives you a Linux environment inside Windows — great if your project uses Linux tools.

---

### Authenticate

```bash
claude
# On first launch, Claude opens your browser for login.
# Log in with the Anthropic account attached to your Pro/Max plan.
```

If you prefer API key authentication:
```bash
export ANTHROPIC_API_KEY=sk-ant-your-key-here
claude
```

### Verify Everything Works

```bash
claude --version # Should show current version
claude doctor # Auto-detects auth, version, MCP health, and permissions
```

---

## 3. Start Project / Ask Questions / Make Code Changes

📖 **Docs:** https://code.claude.com/docs/en/quickstart

> **What you'll learn:** Understand the four natural modes of working with Claude Code — Execute, Ask, Plan, and Edit — and how to write prompts that get the right result in each.
>
> **What you'll build:** Complete working app built — auth with 4 test accounts, dashboard, create group, group detail, add expense with equal split, balance calculation, and soft-delete.


### Four Modes of Interaction

When you work with Claude Code, you're naturally switching between four modes depending on what you need. You don't have to declare which mode you're in — just write the prompt and Claude figures it out.

---

**Execute** — major changes across multiple files, new features, folder setup

This is when you want Claude to take action across the whole codebase. Feature builds, restructuring, setting up new pages. Claude reads what it needs, writes what's required, runs install commands.

```
Build the ability for users to create a group and add members by email.
After creating, redirect to the group's detail page.
```

```
Set up the full folder structure and wire up all routes for Splitmate.
```

---

**Ask** — questions about the codebase, understanding what exists

Use this before building anything you're not sure about. Claude reads the relevant files and explains without changing anything.

```
Where does the balance calculation happen?
How is the current user's session stored?
What happens when a pending member registers?
```

---

**Plan** — think through an approach before writing code

Ask Claude to reason through a feature and outline the steps before touching any file. This is what Plan Mode (§4) formalises — but you can trigger it with words alone too.

```
Plan — don't code yet.
How would you approach adding expense categories to Splitmate?
What files would change and in what order?
```

---

**Edit** — small, targeted changes to existing code

Precise, scoped edits. One thing, in one place, leave everything else alone.

```
On the group detail page, make the expense amount larger and bolder.
```

```
The delete button shows for all members — it should only show for the person who created the expense. Fix that. Don't change anything else.
```

---

### Writing Good Prompts

The mode matters less than the clarity. These rules apply to all four:

```
Describe what the USER sees — not which files to create:
 ❌ "Create an ExpenseCard.jsx component with a delete button prop..."
 ✅ "On the group detail page, each expense should have a delete button"

Say what should happen — not the implementation:
 ❌ "Filter the expenses array using Array.filter with isDeleted check"
 ✅ "Don't show deleted expenses anywhere in the app"

Scope it when you care about blast radius:
 "Fix the balance bug — don't change any other behavior"
 "Only change the visual styling, not the logic"
```

---

### First Prompt — Base Structure

One prompt. Get the skeleton running with clean foundations — everything else builds on top.

```
Create a new web app called "Splitmate" — an expense sharing app for multiple users.

Tech: React, Vite, Tailwind CSS, React Router.

Only create the structure — do not build any features or logic yet.
We will give you more details in the next prompt.

Create stub pages (just a heading and the route name each):
Landing, Login, Register, Dashboard, Create Group, Group Detail.

Wire up the routes:
 / → Landing page
 /login → Login
 /register → Register
 /dashboard → Dashboard
 /group/new → Create Group
 /group/:id → Group Detail

Create the folder structure inside src/:
 components/, context/, pages/, hooks/, utils/, data/

Create an empty src/data/storage.js file — we'll fill it in the next prompt.

Run the dev server.
```

**After Claude starts the server, verify in the browser:**

- Open `localhost:5173` and click through each route — confirm every page shows its title with no errors

Every route shows its title. The shell is running. Nothing is wired up yet — that's the next prompt.

---

### Second Prompt — Complete Working App

Now build everything needed for a working multi-user expense sharing app in one shot. The goal: log in as one user, create a group, add expenses, see the balance — then log in as a second user and see the same group from their perspective.

```
Build the complete Splitmate app on top of the existing structure.

Important: use localStorage only for all data storage — no backend, no server,
no API calls, no mock servers. We will move to Supabase later in the project.
All data lives in the browser. All data goes through src/data/storage.js only —
nothing else in the app should read or write localStorage directly.

--- Auth ---
Users can register (name + email + password) and log in.
Plain text passwords are fine for now — this is local testing only.
Keep all auth logic inside AuthContext — nothing else should touch it directly.
Pre-create 4 test accounts on first load:
 shubham@test.com / password
 bob@test.com / password
 rahul@test.com / password
 eva@test.com / password

--- Landing Page (/) ---
Public page — no login required. Explains what Splitmate does.
Show: a headline, a brief description of the key features (create groups,
track shared expenses, see who owes what), and a "Get started" button → /register.
A "Sign in" link for existing users → /login.
If the user is already logged in, automatically redirect to /dashboard.

--- Dashboard (/dashboard) ---
What logged-in users see after signing in. Show all groups the user belongs to.
Each group card shows: name, member count, and the user's current balance
("You're owed $1,800" in green / "You owe $800" in red / "All settled" in gray).
Top of page: three summary numbers — total owed to you, total you owe, net balance.
Empty state with a "Create group" button if they have no groups.

--- Create Group ---
User enters a group name and adds members one at a time by email.
If the email belongs to a registered user → add as active.
If not → add as pending (they'll get access when they register).
Show a "pending" label next to unregistered members.
Creator is added automatically.
After saving, redirect to that group's detail page.

--- Group Detail ---
Shows the group name, member list (active / pending), and all expenses.
For each expense: description, total amount, who paid, date.
Balance summary at the bottom — from the current user's perspective:
 "You owe Shubham $800" (prominent, red)
 "Bob owes you $200" (prominent, green)
 "Rahul owes Bob $300" (muted — doesn't involve the current user)
Button to add a new expense. Option to delete an expense.

--- Add Expense (modal, not a separate page) ---
When a user clicks "Add expense" in the group detail page, a modal opens on top.
No navigation — the group detail page stays in the background.

Modal fields: what it was for, total amount, date, who paid (dropdown),
who shares it (checkboxes — all selected by default).
Split equally by default — show each person's share live as they type.
Save button closes the modal and the expense appears immediately in the list.
Cancel button closes without saving.

No /group/:id/expense/new route needed — the modal lives inside GroupDetailPage.

--- Balance Calculation ---
Calculate the minimum number of payments to settle all debts in a group.
Example: if Shubham paid for dinner and Bob paid for the cab, end up with
one settlement ("Bob owes Shubham $200") instead of two transfers.
Always exclude soft-deleted expenses from calculations.
Soft delete: when a user removes an expense, set isDeleted: true —
never actually delete the record. Balance always recalculates from scratch.
```

**After Claude finishes, verify in the browser:**

1. Log in as shubham@test.com
2. Create a group "Goa Trip", add bob@test.com
3. Add expense "Hotel" $3000, paid by Shubham, split equally
4. See balance: "Bob owes you $1,500"
5. Log out → log in as bob@test.com
6. See "Goa Trip" on the dashboard
7. Open the group — see the expense and "You owe Shubham $1,500"

---

### Ask Questions and Make Small Edits

Once the app is running, use Claude Code to explore and refine — not just build.

**Ask questions to understand what was built:**
```
Walk me through how the pending member flow works — what happens
when someone who was added as pending actually registers?

Where exactly is the balance calculated? Show me the logic.

What would happen if two expenses in the same group have different currencies?
```

**Make small targeted edits:**
```
The balance section is hard to read. Make amounts larger and put
"You owe" items in red and "You're owed" items in green more clearly.

The group list on the dashboard shows creation date. Remove that —
show the number of expenses instead.

When I delete an expense, there's no confirmation. Add a simple
"Are you sure?" dialog before deleting.
```

These short prompts are just as important as the big ones — they're where you tune the app to feel right.

---

### Third Prompt — Design System

With a fully working app, now make it look like a real product. This prompt replaces all default Tailwind aesthetics with a deliberately designed system. Be specific — vague prompts produce generic results.

```
Redesign Splitmate's visual identity. The current version looks like
default Tailwind components. We need it to feel like a real product.
Do not change any logic, routing, or data — only visual styling.

━━━ COLOR SYSTEM ━━━

Background: #F8F7F4 (warm off-white — not pure white, not gray)
Surface: #FFFFFF (cards, modals, inputs)
Border: #E8E4DE (all borders — one consistent value everywhere)

Primary: #EA580C (orange-600 — warm, energetic, associated with money and action)
Primary hover:#C2410C (orange-700)
Primary text: #FFFFFF

Text primary: #1C1917 (stone-900 — warmer than gray-900)
Text secondary: #78716C (stone-500)
Text muted: #A8A29E (stone-400 — for timestamps, labels)

Financial colors — use these ONLY for balance/money states:
 Positive (owed to you): background #ECFDF5, text #065F46 (emerald)
 Negative (you owe): background #FEF2F2, text #991B1B (red)
 Neutral (settled): background #F5F5F4, text #57534E (stone)

Danger (delete, destructive): #DC2626

━━━ TYPOGRAPHY ━━━

Font: Inter (import from Google Fonts)

Size scale (use only these, nothing else):
 xs: 12px / 400 — timestamps, meta labels
 sm: 14px / 400 — body text, form labels, secondary content
 base: 16px / 400 — default body
 lg: 18px / 600 — card titles, section headings
 xl: 24px / 700 — page headings
 2xl: 30px / 800 — large financial amounts on dashboard

Financial amounts always use:
 font-variant-numeric: tabular-nums (so digits align in columns)
 font-weight: 700 or 800

━━━ SPACING & LAYOUT ━━━

Max page width: 680px, centered, horizontal padding 16px
Section gap: 32px between major sections
Card internal padding: 20px
Gap between list items: 8px

Navbar: height 56px, border-bottom 1px solid #E8E4DE, bg white, sticky top

━━━ COMPONENT RECIPES ━━━

Cards:
 background: white
 border: 1px solid #E8E4DE
 border-radius: 12px
 padding: 20px
 NO box-shadow — use the border instead

Buttons:
 Primary: bg #EA580C, text white, border-radius 8px, px-4 py-2.5,
 font-weight 500, font-size 14px
 Hover: bg #C2410C
 No shadow, no gradient
 Secondary: bg white, border 1px solid #E8E4DE, text #1C1917,
 same sizing as primary
 Danger: bg #DC2626, text white — same sizing

Inputs:
 border: 1px solid #E8E4DE
 border-radius: 8px
 padding: 10px 14px
 font-size: 14px
 background: white
 Focus: border-color #EA580C, ring: 0 0 0 3px rgba(234,88,12,0.12)
 No shadow

Badges (member status):
 Active: bg #ECFDF5, text #065F46, font-size 12px, px-2 py-0.5, rounded-full
 Pending: bg #FFFBEB, text #92400E, same sizing

Balance pills (in group cards):
 Use the financial colors above — colored background + matching text
 border-radius: 999px (fully rounded), px-3 py-1, font-size 13px, font-weight 600

Modal:
 Overlay: rgba(0,0,0,0.4)
 Modal box: white, border-radius 16px, max-width 480px, padding 24px
 Header: title left, × button right, border-bottom 1px solid #E8E4DE, pb-4 mb-4

━━━ VISUAL RULES (these prevent the AI-generated look) ━━━

1. The primary orange is used ONLY for: primary buttons, focused inputs,
 links, active states. Everything else is black, white, or stone.
 Resist using orange for headings or decorative elements.

2. Financial amounts are always larger and bolder than surrounding text.
 "$1,800" should visually dominate its card — not blend in.

3. The balance summary in Group Detail is the most important UI element.
 Settlements involving the current user go first, bold and full-color.
 Settlements not involving them go below, in stone/muted styling.

4. Empty states: centered, stone-400 text, no icon unless it's a custom
 SVG. No emoji. No generic illustrations.

5. No gradients, no shadows on buttons, no glow effects.
 Depth comes from the border and background contrast only.

6. Member avatars: circle, 28px, bg stone-100, text stone-600,
 initials in 11px font-weight 600. No colored backgrounds per-user.

7. Navbar: logo in font-weight 700, 16px. Right side: user's name in
 14px stone-500, then a "Sign out" text link in stone-400. No icon buttons.

8. Every page has the same warm off-white (#F8F7F4) background.
 Only cards and modals are white.

Apply these styles consistently across all pages. After applying,
check the dashboard and group detail page in particular — if the balance
amounts don't look like the most prominent thing on the screen, increase
their size and weight.
```

**Why this is specific enough to work:** instead of "think Monzo" (which Claude can't see), the prompt gives exact hex values, font weights, and explicit rules like "orange is used ONLY for primary buttons." The "visual rules" section directly counteracts the patterns that make apps look AI-generated — overuse of the brand color, flat financial amounts, random shadows.

---


## 4. Plan Mode

📖 **Docs:** https://code.claude.com/docs/en/extended-thinking

> **What you'll learn:** Use Plan Mode to make Claude think and outline before coding — agreeing on scope before any file is touched.
>
> **What you'll build:** Unequal expense split added — users can enter custom amounts per person in the Add Expense modal.


### What Is Plan Mode?
Plan Mode makes Claude **think and outline before touching any code**. Claude creates a step-by-step plan and waits for your approval. This prevents going down the wrong path on large tasks.

### How to Activate

```bash
# Method 1: Keyboard shortcut (inside a Claude Code session)
Shift + Tab # Toggles Plan Mode on/off

# Method 2: Use planning language in your prompt
Plan (don't code yet): How would you approach this?
Think step-by-step about...
Before writing anything, outline...
```

### When to Use Plan Mode

**Use it for:**
- Features that touch multiple pages and utilities
- Anything with a "right approach" question before you start
- When you want to review the architecture before committing to it

**Skip it for:**
- Single-file bug fixes
- Adding a visual tweak
- Renaming something

---

### Building with Plan Mode — Unequal Expense Split

The app currently splits expenses equally. Now we want to let users enter custom amounts per person. This touches the Add Expense form, the expense data model, and the balance calculation — Plan Mode helps get the architecture right before writing a single line.

**Step 1 — Activate Plan Mode and describe the feature:**

```
Shift + Tab (to enter Plan Mode), then:

We want to add unequal expense splitting to Splitmate.

Right now every expense splits the total equally among all members.
We want to let users enter a custom amount per person instead.

The app currently stores expenses with a splits array:
 splits: [{ memberId, amount }]

The balance calculation reads splits[].amount directly.

Think through:
1. What changes in the Add Expense modal (UI for equal vs manual split)
2. How to validate that manual amounts add up to the total
3. Whether the data model needs to change
4. What the balance.js utility needs to handle differently (if anything)
5. Edge cases: what if the user switches between equal and manual mid-form?

Do NOT write any code yet. Just outline the plan.
```

**Claude responds with a plan — something like:**

```
Plan for unequal expense splitting:

1. Add Expense Modal — toggle between "Split equally" and "Split manually"
 Equal: auto-calculate splits[] as amount ÷ member count, show per-person preview
 Manual: show a number input per member, live counter showing $X remaining
 Validation: total of all manual inputs must equal expense amount before save

2. Data model — no change needed
 splits[] already stores per-person amounts — works for both equal and manual

3. balance.js — no change needed
 It already reads splits[].amount directly, so it handles manual splits correctly

4. Edge case: switching mode clears the previous split and recalculates from scratch

Shall I proceed?
```

**Step 2 — Approve and implement:**

```
Yes, proceed with the plan.
```

Claude now writes the code — the unequal split feature, built correctly in one pass because the approach was agreed upfront.

**Step 3 — Verify in the browser:**

- Log in as shubham@test.com and open the Goa Trip group
- Add an expense "Scuba Diving" $6000, paid by Shubham
- Switch to manual split: enter $3000 for Shubham, $2000 for Bob, $1000 for Rahul
- Save and check that the balance updates correctly for all three users

---

### Why Plan Mode Saved Time Here

Without Plan Mode, you might have prompted "add unequal split support" and Claude could have changed the data model unnecessarily, or added complexity to `balance.js` that wasn't needed. The plan surfaced that neither needed to change — only the form UI. That's the value: **agreeing on scope before writing code**.

---

### Ask Questions and Make Small Edits

After building the unequal split, use the Edit mode to fix two bugs you'll notice when testing.

---

**Bug 1 — Balance resets when a second expense is added**

When you add a second expense, the balance from the first expense disappears. The total should accumulate across all expenses, not just show the latest one.

```
Bug: when I add a second expense the balance from the first one disappears.

Example: group of 2 people, I add Flight $1200 (split equally — Bob owes me $600).
Then I add Dinner $100 with a custom split (Bob owes me $80).
Now the balance shows Bob owes me $80 — it should show $680.

The balance should accumulate across all expenses in the group, not reset each time.
Look at balance.js and fix the calculation. Don't change anything else.
```

---

**Bug 2 — Pending members excluded from expense splits**

When adding an expense in a group with pending members, the pending member is excluded from the split. They should always be included — a pending member still owes their share, they just haven't registered yet.

```
Bug: when I add an expense in a group of 4 where one member is pending,
the expense is only split between the 3 active members. The pending member
is excluded from the split entirely.

Pending members should always be included in expense splits.
Their status (active vs pending) only affects whether they can log in —
it has nothing to do with whether they owe money.

Fix the AddExpenseModal so all group members (active and pending) are
included in the split by default. Don't change anything else.
```

---

## 5. Slash Commands

📖 **Docs:** https://code.claude.com/docs/en/cli-reference

> **What you'll learn:** Learn the built-in slash commands — /model, /compact, /clear, /cost, /review — and how to use them in a real session flow.
>
> **What you'll build:** No new feature — demonstrates a productive session using the commands on the existing codebase.


### What Are Slash Commands?
Slash commands are **built-in shortcuts** that trigger specific behaviors. Type `/` to see the full list.

### Core Commands

```bash
/help # Show all available commands
/clear # Wipe conversation — fresh start
/compact # Compress conversation to free context
/cost # Token usage for this session
/status # Session info, model, context summary
/model # Switch model or open model picker
/effort # Set reasoning depth (low/medium/high/xhigh)
/exit # Exit Claude Code
```

### Development Commands

```bash
/review # Review recent changes for issues
/fix # Fix the last error or issue mentioned
/explain # Explain the code Claude just wrote
/test # Run your test suite (if configured)
```

### Practical Session Workflow

```bash
claude # Start session

/model opus # Use Opus for architecture planning
Plan the data layer for Splitmate...

/model sonnet # Switch to Sonnet for implementation
Implement the plan...

/cost # Check spend
# → "Session: 14,200 tokens (~$0.06)"

/compact # Context getting long — compress

/model haiku # Haiku for a simple task
Add aria-label to the delete button in ExpenseCard.jsx

/model sonnet # Back to Sonnet for real work
Build the ReportsPage...

/review # Review everything after feature is done

/clear # Done for the day — fresh start tomorrow
```

### The `/review` Command in Practice

After building a feature, run `/review` and Claude examines your recent changes:

```bash
/review

# Claude flags:
# ✅ Soft-delete correctly implemented
# ✅ Balance filters out isDeleted expenses
# ⚠️ ReportsPage doesn't handle the case where there are no expenses
# ⚠️ Chart.js not added to package.json yet
# ❌ The CSV download uses array index as key — will create duplicate rows
# ❌ Missing try/catch around localStorage in useExpenses hook
```

---

## 6. Session & Conversation Management

📖 **Docs:** https://code.claude.com/docs/en/session-management

> **What you'll learn:** Understand how sessions work — how to start, resume, name, and exit conversations so you never lose context between working sessions.
>
> **What you'll build:** No new feature — workflow skill used throughout every lecture from here on.


### What Is a Session?

Every time you work with Claude Code, you're inside a **session** — the full conversation history including every prompt you sent, every file Claude read, every tool it used, and every response. Sessions are saved to disk automatically.

This means you can close the terminal, come back the next day, and pick up exactly where you left off — all the context is still there.

---

### Starting a Session

```bash
claude # start a fresh session
claude -c # continue the most recent session
claude --continue # same, long form
```

`claude -c` is the one you'll use most. Close the terminal, come back later, type `claude -c` — you're right back where you were with full context.

---

### Listing and Resuming Sessions

```bash
claude -r
claude --resume
```

Opens an interactive picker showing all your recent sessions. Navigate the list and select one to resume it. All context from that session comes back — files Claude read, decisions made, everything.

Resume a specific session directly:

```bash
claude --resume abc123def # by session ID
claude --resume "supabase-migration" # by session name
```

---

### Naming a Session

Inside any session, use `/rename` to give it a clear, findable name:

```
/rename add-expense-categories
```

Do this early in a session — "feature-group-settings" is findable weeks later, "explain this function" is not.

Good naming for Splitmate:

```
/rename feature-group-settings
/rename bugfix-balance-calculation
/rename supabase-migration
/rename refactor-storage-layer
```

You can also rename from inside the `/resume` picker — press `R` on any session in the list.

---

### Exiting a Session

```
/exit
```

Cleanly ends the session. Claude finishes what it's doing before closing. The session is saved and resumable later.

---

### The Full Workflow

```bash
# Day 1 — start a named session
claude
/rename feature-group-budget
# ... build the feature ...

# Day 2 — pick up where you left off
claude -r
# select "feature-group-budget" from the list
# Claude has full context — no re-explaining needed
```

---

### Session vs /clear vs /compact

| Command | What it does | Context after |
|---|---|---|
| `claude -c` | Continue the most recent session | Full history |
| `claude -r` | Pick any saved session to resume | Full history of that session |
| `/compact` | Compress the conversation | Summary preserved |
| `/clear` | Wipe the current conversation | Blank slate |
| `/exit` | End and save the session | Saved — resumable via `claude -r` |

---


## 7. Models & Usage

📖 **Docs:** https://code.claude.com/docs/en/model-config

> **What you'll learn:** Understand when to use Haiku, Sonnet, Opus, and Fable — and how to switch models mid-session based on task complexity.
>
> **What you'll build:** Four refinements across three models: expense split breakdown per card (Haiku), pending member balance bug (Sonnet), expense edit flow (Sonnet), full balance scenario audit with fixes (Opus + high effort).


### Current Models (July 2026)

| Model | Speed | Intelligence | Best For |
|---|---|---|---|
| **Haiku 4.5** | Fastest | Good | Simple edits, quick lookups, small UI tweaks |
| **Sonnet 5** | Balanced | Great | **Daily coding work — new default** |
| **Opus 4.8** | Slower | Excellent | Architecture, hard bugs, long agent sessions |
| **Fable 5** | Slower | Frontier | Days-long complex tasks, large migrations, highest capability |

**The model family as of July 2026:**

Claude now has four tiers. Haiku → Sonnet → Opus are the familiar tiers. **Fable** is a new Mythos-class tier that sits above Opus — Anthropic's most capable publicly available model, released June 9, 2026.

**Model aliases:**
```bash
/model haiku # → Haiku 4.5
/model sonnet # → Sonnet 5
/model opus # → Opus 4.8
/model fable # → Fable 5
```

---

### What Is Fable 5?

Claude Fable 5 is Anthropic's most capable widely released model, built for the most demanding reasoning and long-horizon agentic work.

The key difference from Opus: the longer and more complex the task, the larger Fable 5's lead over other models. For a 10-minute task, Opus 4.8 is fine. For a task that runs for hours — migrating an entire codebase, a multi-day agent session — Fable 5 is where the difference shows.

Fable 5 includes safety classifiers that can decline certain requests in areas like cybersecurity, biology, and chemistry — routing those to Opus 4.8 instead. This affects fewer than 5% of sessions.

**For Splitmate:** Fable 5 is overkill for the features we're building. Opus 4.8 is the right ceiling for this course. But it's worth knowing Fable exists for when you graduate to larger, more complex projects.

**Pricing:** $10/$50 per million input/output tokens — double Opus 4.8. Use it when the task justifies it.

---

### Sonnet 5 — The New Daily Driver

Sonnet 5 is the most agentic Sonnet yet and the new default on Free and Pro plans. It scores 63.2% on SWE-bench Pro (closing in on Opus 4.8's 69.2%) and drives terminals and browsers autonomously.

For most Claude Code work — building features, fixing bugs, refactoring — Sonnet 5 is what you'll use. It replaced Sonnet 4.6 as the default.

---

### Switching Models

```bash
# Start with a specific model:
claude --model claude-sonnet-5

# Switch during a session:
/model haiku # cheap, fast
/model sonnet # daily driver
/model opus # heavy lifting
/model fable # frontier tasks
```

---

### When to Use Each Model

**Haiku 4.5 — Fast, cheap:**
```
Add a loading spinner to the button
Fix the Tailwind class on mobile view
Rename this variable across the file
Show each person's split amount on the expense card
```

**Sonnet 5 — Daily driver (use most of the time):**
```
Build the CreateGroup page with form validation
Implement the balance calculation utility
Add soft-delete support to all expense operations
Build the Reports page with a bar chart
Debug why expenses aren't loading
```

**Opus 4.8 — Heavy lifting:**
```
Review the entire codebase for edge cases and bugs
Architect the localStorage → Supabase migration strategy
Figure out why balance gives wrong results with 5+ members
Orchestrate agent teams to build multiple features in parallel
```

**Fable 5 — Frontier tasks:**
```
Migrate an entire 50k-line codebase to a new framework
Run a days-long autonomous agent session with minimal oversight
Complex multi-stage refactors that previous models couldn't sustain
```

---

### Effort Levels

```bash
/effort low # Fast — simple tasks
/effort medium # Standard reasoning
/effort high # Deeper analysis (Sonnet 5 default)
/effort xhigh # Maximum reasoning (Opus 4.8 default)
```

Fable 5 automatically applies maximum effort — the `/effort` flag has no effect on it.

---

### Monitoring Usage

```bash
/cost # Token count and estimated cost for current session
/status # Session info, model, context usage
```

---

### Building Splitmate — Picking the Right Model

The app is working. Now we refine it. Each of these tasks calls for a different model — the wrong choice wastes money or time.

---

**Task 1 — Show expense split breakdown per expense (Haiku)**

Pure UI addition, no logic changes. Haiku is the right choice — fast, cheap, no reasoning needed.

```bash
/model haiku
```

```
On each expense card in the Group Detail page, show how the expense was split.

Two options — pick the simpler one to implement:

Option A: expand inline below the expense description
 Show each person's share on a new line:
 Shubham: $600 Bob: $600 Rahul: $300

Option B: a small "i" button on each expense card
 Tap it to expand/collapse the split breakdown below that card

Either way, show each member's name and their exact split amount.
Use the splits[] array already on the expense — no data changes needed.
Keep it small and subtle — this is secondary information.
```

Haiku gets it done in seconds — cheap and fast for a simple UI addition.

---

**Task 2 — Fix the pending member balance bug (Sonnet)**

A focused logic bug touching balance calculation and member ID linking. Sonnet's territory.

```bash
/model sonnet
```

```
Bug: when a pending member registers and logs in for the first time,
they can see the group but their balance shows as zero — as if everything
is already settled.

The expense splits were calculated correctly when added (their member record
was included), but now that they've registered, the balance view isn't
recognising their member record as belonging to them.

The issue is likely in how we match a logged-in user's userId to their
member record in the group — when the member was pending, userId was null.
After registration, linkPendingMembers sets userId, but the balance display
may still be looking for an unmatched userId.

Look at balance.js and GroupDetailPage.jsx. Fix the member ID matching so
newly registered members see their correct outstanding balance.
Don't change anything else.
```

---

**Task 3 — Add expense edit flow (Sonnet)**

Touches the form, the group detail page, and storage. Multi-file feature work — Sonnet.

```bash
/model sonnet
```

```
Add the ability to edit an existing expense.

When a user taps "Edit" on an expense in the group detail page:
- Open the Add Expense modal pre-filled with the existing values
- When they save: mark the old expense as deleted and save a new one with the changes
 (this keeps the history clean and balance calculations accurate)
- If they cancel: nothing changes

The edit button should only appear for the person who created the expense.
```

---

**Task 4 — Full calculation audit (Opus + high effort)**

This is where Opus earns its cost — hold the entire app's logic in context, think through edge cases, catch things that slipped through earlier fixes.

```bash
/model opus
/effort xhigh
```

```
Review all balance calculations and money-related logic in Splitmate.

We've fixed a few bugs already:
- Balance was resetting after each new expense (fixed in §4)
- Pending members were excluded from splits (fixed in §4)
- Pending members saw zero balance after registering (fixed above)

Now do a thorough check for anything we may have missed.

Think through every scenario:
- Groups with 1 person
- Groups with 4+ people
- Expenses where paidBy person is not in the split
- Multiple expenses paid by different people
- A mix of equal and manual splits in the same group
- An expense that gets edited (old marked deleted, new created)
- Settled expenses — does the balance still update correctly?
- Pending member joins after several expenses already exist
- What happens if a member is removed from the group?

For each scenario: describe what should happen, then check if the code
handles it correctly. Report any issues with file and line number.
Then fix the ones that are bugs — not style issues or edge cases
that don't affect correctness.
```

Review what Opus reports before it fixes — this is a good moment to pause and understand what it found.

---

**Task 5 — When would we use Fable 5? (Discussion)**

We won't run this — Fable 5 is overkill for Splitmate's individual features. But it's worth understanding when it earns its cost.

Fable 5 is designed for tasks where the complexity compounds over time — the longer and more complex the task, the larger its advantage over Opus. For Splitmate, the task that comes closest would be the full Supabase migration (§16) — reading the entire localStorage schema, designing a Postgres schema, writing RLS policies, updating auth, and updating the data layer, all in one continuous session.

**The question to ask you:**

*"At what point in this course could we have used Fable 5 instead of Opus? What would have been different?"*

Good answers:
- The §16 MCP migration — multi-step, high-stakes, touches every layer of the app
- The §7 full balance audit with `/effort xhigh` — Fable 5 at default effort would match Opus at max effort
- Building the entire app in §3 — though Sonnet 5 handles it fine, Fable 5 would have required fewer follow-up corrections

**When Fable 5 starts making sense:**
```
A migration touching 20+ files across the entire codebase
A multi-day autonomous agent session with minimal oversight
Debugging a complex race condition that Opus couldn't isolate
Refactoring a 50k-line codebase to a new architecture
```

**The cost reality:**
```
Haiku 4.5: $0.25 / $1.25 per million tokens → daily small tasks
Sonnet 5: $3 / $15 per million tokens → daily coding
Opus 4.8: $5 / $25 per million tokens → heavy lifting
Fable 5: $10 / $50 per million tokens → frontier tasks only
```

For Splitmate — Sonnet 5 for most work, Opus 4.8 for architecture and audits. Fable 5 when you graduate to larger, longer-running projects.

---


## 8. When Claude Gets It Wrong

📖 **Docs:** https://code.claude.com/docs/en/checkpointing

> **What you'll learn:** Know how to interrupt, rewind, and recover when Claude misunderstands a task or takes the wrong approach — without losing your work.
>
> **What you'll build:** No new feature — essential recovery skills used throughout every lecture.


### It Will Happen — Here's How to Handle It

Claude misunderstands requirements, touches files you didn't want changed, or goes in a completely wrong direction. Knowing how to recover quickly is as important as knowing how to prompt well.

---

### 1. Interrupt Mid-Task — Press Escape

If Claude is doing something wrong and you catch it early, press `Escape`. Claude stops immediately — mid-sentence, mid-file-write.

```
# Claude is rewriting three files when you only wanted one changed
→ Press Escape
→ "Stop — only change AddExpenseModal.jsx. Don't touch storage.js or GroupDetailPage."
→ Claude acknowledges and restarts with the correct scope
```

Catching it early means less to undo. The moment something looks wrong, stop it.

---

### 2. Rewind — Claude Code's Built-in Checkpoint System

This is the most important recovery tool. Claude Code **automatically creates a checkpoint after every single prompt** — no setup needed. You can roll back to any previous state in the session.

```bash
/rewind
# Also works as:
/checkpoint
```

Running `/rewind` shows a list of every conversation turn with timestamps. Pick the point before things went wrong — Claude restores both your files and the conversation to that exact state.

**What happens after rewinding:**

The conversation "forks" from that point. All messages after your chosen checkpoint are gone, your files are restored to that state, and you can try a different prompt. It's exactly like Cursor's checkpoint restore.

**The file snapshot system:**

After each response, Claude Code silently snapshots every file it touched. These snapshots are incremental — only changed files get a new version. This is completely automatic.

```
Prompt 1: Claude adds the notes field → snapshot saved
Prompt 2: Claude adds expense categories → snapshot saved
Prompt 3: Claude rewrites balance.js completely — wrong!
→ /rewind → pick Prompt 2's checkpoint
→ Files restored, balance.js is back, categories are still there
→ Re-prompt with better instructions
```

**Disabling it (rarely needed):**

```bash
export CLAUDE_CODE_DISABLE_FILE_CHECKPOINTING=1
```

**Rewind vs Git:**

| | `/rewind` | Git |
|---|---|---|
| Scope | Within current session only | Across all sessions |
| Setup | None — fully automatic | Requires commits |
| Granularity | Every single prompt | Every commit you make |
| Survives `/clear` | No | Yes |

Use `/rewind` for immediate recovery within a session. Use git for longer-term safety nets.

---

### 3. Git — Safety Net for Bigger Mistakes

For mistakes that span multiple sessions, or if you want a checkpoint before a risky operation:

```bash
# Before a risky prompt — commit what you have:
!git add . && git commit -m "checkpoint before refactor"

# Claude does something wrong:
!git checkout . # restores all files to last commit
```

**Good times to commit before prompting:**

```
Before a major refactor
Before asking Claude to "restructure" or "clean up" anything
Before a design overhaul
After each feature works correctly
```

---

### 4. Narrow the Scope When Claude Keeps Getting It Wrong

If Claude repeatedly misunderstands a prompt, the problem is usually the prompt — not Claude.

```
# Too broad — Claude might change too much:
"Fix the balance calculation"

# Better — scope it explicitly:
"Look at only src/utils/balance.js.
The issue is that deleted expenses are included in the total.
Add a filter for isDeleted === false. Don't change anything else."
```

**Other techniques:**

```
# Tell Claude what NOT to do:
"Add the category field — do not touch the balance calculation"

# Ask for a plan first (uses Plan Mode from §4):
"Plan how you'd add expense categories — don't write any code yet"

# Start smaller:
"Just show me what you would change in storage.js — don't edit it yet"
```

---

### 5. Just Tell Claude What Went Wrong

```
That's not what I wanted. Let me explain what I actually need.
```

Claude doesn't take it personally. Describe what went wrong and what the correct outcome should be. This is faster than undoing and restarting most of the time.

---

### Recovery Decision Guide

| Situation | What to do |
|---|---|
| Claude is doing something wrong right now | Press Escape immediately |
| Claude finished but the result is wrong (same session) | `/rewind` to before the bad prompt |
| Claude changed files across multiple sessions | `git checkout .` to last commit |
| Claude keeps misunderstanding the same prompt | Rewrite the prompt — narrow the scope |
| Context is confused but the work is good | `/compact` — preserves progress |
| Starting a completely different task | `/clear` — fresh slate |

---


## 9. CLAUDE.md

📖 **Docs:** https://code.claude.com/docs/en/memory

> **What you'll learn:** Learn what CLAUDE.md is, how to create it, and what to put in it to give Claude persistent project memory — done early so every lecture from §10 onwards benefits from it.
>
> **What you'll build:** CLAUDE.md written for Splitmate — data models, routes, conventions, anti-patterns, and the current build status checklist.


### What Is CLAUDE.md?
`CLAUDE.md` is a markdown file that **Claude Code reads automatically at the start of every session**. It's your project's memory — the one file that survives `/clear` and tells Claude what it needs to know about your project without re-explaining it every time.

Think of it as an onboarding doc written for Claude, not for humans.

### Where to Put It

```
splitmate/
├── CLAUDE.md ← always read, covers the whole project
└── src/
 └── CLAUDE.md ← read when working inside /src (optional, for larger projects)
```

---

### How to Create a CLAUDE.md

**Option 1 — Let Claude generate it**

Claude Code has a built-in `/init` command that reads your project and creates a CLAUDE.md for you:

```bash
/init
```

Claude scans your folder structure, reads `package.json`, and generates a starter CLAUDE.md. You then edit it to add your own conventions.

**Option 2 — Create it manually**

Just create the file yourself and write what you want Claude to always know:

```bash
touch CLAUDE.md
# Then open it in your editor and fill it in
```

**Option 3 — Ask Claude to write it**

```
Read the current project structure and create a CLAUDE.md for Splitmate.
Include: tech stack, folder structure, data models, routes, key conventions.
```

---

### What to Put In It

Keep it **focused and short**. Claude reads it every session — if it's 300 lines long, it eats context budget before you've typed anything. Aim for 50–80 lines covering only what Claude genuinely needs to not make mistakes.

A useful CLAUDE.md covers:

- **What the project is** — one or two sentences
- **Tech stack** — so Claude picks the right libraries
- **Folder structure** — so Claude puts new files in the right place
- **Key data shapes** — so Claude understands your objects
- **Conventions** — what to do and what NOT to do
- **Current status** — what's done and what's next

---

### Root CLAUDE.md for Splitmate

```
# Splitmate

Multi-user expense sharing app. Users register, log in, create groups,
add expenses, and track who owes whom across all their groups.
Phase 1: localStorage. Phase 2: Supabase Auth + PostgreSQL.

## Stack
React 18 · Vite · React Router v6 · Tailwind CSS · localStorage (Phase 1)

## Design system
Primary colour: #EA580C (orange-600)
Background: #F8F7F4 (warm off-white — every page)
Surface: #FFFFFF (cards, modals, inputs)
Border: #E8E4DE (all borders — one value everywhere)
Positive (owed to you): bg #ECFDF5 text #065F46
Negative (you owe): bg #FEF2F2 text #991B1B
Neutral (settled): bg #F5F5F4 text #57534E
Font: Inter · Tailwind only — no inline style={} · no .css files

## Folder structure
src/
 components/ → Navbar, Footer, ProtectedRoute, reusable UI (Button, Card, Badge)
 context/ → AuthContext.jsx — provides useAuth() hook
 pages/ → one file per route
 hooks/ → useGroups.js, useExpenses.js, useGroupBalances.js
 utils/ → balance.js, formatters.js
 data/ → storage.js — ALL localStorage access goes here, nowhere else

## Routes
 Public: / (landing page) /login /register
 Protected: /dashboard /group/:id /group/new

 / → redirects to /dashboard if already logged in
 ProtectedRoute redirects to /login if getCurrentUser() is null.
 Login and Register redirect to /dashboard on success.

## Data models

### User (localStorage: sm_users)
{ id, name, email, password, createdAt }
password is plain text — Phase 1 local testing only.
Never include password in currentUser or pass it to any component.

### Session (localStorage: sm_currentUser)
{ id, name, email } — no password

### Group (localStorage: sm_groups)
{
 id, name, createdBy,
 members: [{ id, email, name, userId, status, joinedAt }],
 createdAt
}
member.status: "active" | "pending"
member.userId: null when the person hasn't registered yet

### Expense (localStorage: sm_expenses)
{
 id, groupId, description, amount, paidBy, date,
 splits: [{ memberId, amount }],
 createdAt, updatedAt, isDeleted
}
paidBy = member.id (group-scoped record ID, NOT userId)
splits[].memberId = same group-scoped member.id
splits[].amount = explicit per-person share (equal or manual)

### Balance (computed — never stored)
calculateNetBalances(expenses, members) → { memberId: netAmount }
minimizeTransactions(netBalances) → [{ from, to, amount }]
Always filter: expenses.filter(e => !e.isDeleted) before passing in.

## Test accounts (auto-seeded on first load)
shubham@test.com / password
bob@test.com / password
rahul@test.com / password
eva@test.com / password

## storage.js functions
Auth:
 getUsers() / saveUsers(users)
 getCurrentUser() → Session | null
 setCurrentUser(session) / clearCurrentUser()
 findUserByEmail(email) → User | null
 linkPendingMembers(userId, email)
 seedTestUsers() → seeds 4 accounts if sm_users is empty

Data:
 getGroups() / saveGroups(groups)
 getExpenses() / saveExpenses(expenses)
 generateId() → crypto.randomUUID()

## AuthContext
Exposes: { user, isAuthenticated, register, login, logout }
Access via: const { user } = useAuth()
Never read localStorage directly — always use useAuth().

## Key rules — violations cause bugs
- Never access localStorage directly — only through storage.js
- Never put password in currentUser, session, or any component prop
- Always filter before balance math: expenses.filter(e => !e.isDeleted)
- Never hard-delete expenses: set isDeleted: true, updatedAt: now()
- Always call linkPendingMembers on every register path
- Dashboard filter: group.members.some(m => m.userId === user.id)
- paidBy and splits[].memberId are member.id (group-scoped) — NOT userId
- balance.js reads splits[].amount directly — never divides expense.amount

## Unequal splits
AddExpenseModal has "Split equally" / "Split manually" toggle.
Equal: splits[] auto-calculated as amount / member count.
Manual: user enters per-person share. Sum must equal total — show "$X remaining".

## Status
- [x] Scaffold (Vite + Router + Tailwind + design system)
- [x] Navbar with logo, Footer
- [x] storage.js + seedTestUsers + linkPendingMembers
- [x] AuthContext (register/login/logout)
- [x] ProtectedRoute, LandingPage, LoginPage, RegisterPage
- [x] Dashboard (all groups + 3-stat summary bar)
- [x] CreateGroupPage (email-based members, active + pending)
- [x] GroupDetailPage (expense list + perspective-aware balance)
- [x] AddExpenseModal (equal + manual split toggle)
- [x] balance.js + useGroupBalances
- [x] Unequal expense split
- [x] Edit expense (delete old + create new)
- [ ] Expense notes field
- [ ] Expense categories
- [ ] GroupSettings (/group/:id/settings)
- [ ] Settle Up button
- [ ] Reports page (/reports)
- [ ] Group Budget
- [ ] Profile page (/profile)

## Known issues
Phase 1 localStorage is per-browser — two users on different devices can't share data.
This is intentional. Use shubham/bob/rahul/eva in the same browser to test.
Supabase migration (Phase 2) fixes the cross-device problem.
```

---

### Backend CLAUDE.md Example

```
# Splitmate API

Express REST API. Supabase for database and auth.

## Stack
Node.js 20 · Express 5 · Supabase · Zod validation · Jest + Supertest

## Folders
routes/ → groups.js, expenses.js, balances.js
middleware/ → authenticate.js (JWT), validate.js (Zod), errorHandler.js
db/ → supabase.js (client), queries.js

## Auth
All protected routes use authenticate.js → reads JWT → sets req.user = { id, email }

## API conventions
GET → 200 + { data }
POST → 201 + { data }
DELETE → 204 (soft-delete: sets is_deleted = true)
Errors → { error: "message" }

## Rules
- Never SELECT * — always name columns
- All expense queries must WHERE is_deleted = false
- Never throw raw DB errors — sanitize in errorHandler
- linkPendingMembers runs as DB trigger on auth.users INSERT
```

---

### Keeping CLAUDE.md Updated

Run `/update-claude-md` at the end of each session:
```bash
/update-claude-md
```

Or ask directly:
```
Update the status checklist in CLAUDE.md — mark CreateGroupPage as done.
Add the pending member convention we established today.
```

---

## 10. Tools

📖 **Docs:** https://code.claude.com/docs/en/tools-reference

> **What you'll learn:** Understand all the tools Claude Code has access to — Read, Write, Edit, Bash, Search, WebSearch, Task — and see each one in action.
>
> **What you'll build:** No new feature — illustrates each tool using the existing Splitmate codebase.


### What Are Tools?
Tools are the **actions Claude can take** beyond responding with text. Every file read, command run, or search performed is a tool.

### Core Built-in Tools

| Tool | What It Does | Real Example | Permission Required |
|---|---|---|---|
| `Read` | Reads a file | Reading `src/utils/balance.js` | No — runs silently |
| `Write` | Creates a new file | Writing a new component from scratch | Yes — prompts first time |
| `Edit` | Targeted edits to an existing file | Changing one function inside a file | Yes — prompts first time |
| `Bash` | Runs shell commands | `npm install`, `npm run dev`, `git status` | Yes — prompts per command |
| `Search` | Searches across files for patterns | Finding all uses of a function | No — runs silently |
| `WebSearch` | Searches the web | Looking up Tailwind docs | No — runs silently |
| `WebFetch` | Fetches a URL | Reading a documentation page | No — runs silently |
| `TodoWrite` | Manages an internal task list | Breaking a big task into subtasks | No — runs silently |
| `Task` | Spawns a subagent | Delegating parallel work | No — runs silently |

**Read-only tools never prompt** — `Read`, `Search`, `WebSearch`, `WebFetch`, `TodoWrite`, `Task` run silently.
**Write and Edit prompt once per session** — press `A` (approve always) and they stop asking.
**Bash prompts for each new command** — `npm install` and `npm run build` are treated as different commands. Once you approve `[A]`, that specific command is remembered.

You'll configure all of this in detail in the next lecture — Permissions (§12).

### Watching Tools in Action

```
◆ Reading file: src/utils/balance.js
◆ Editing file: src/pages/GroupDetailPage.jsx
◆ Running bash: npm install recharts
◆ Searching: "isDeleted" in src/
◆ Web search: "recharts bar chart with custom tooltip"
```

### Tool Breakdown — Splitmate Examples

| Tool | You say | Claude does |
|---|---|---|
| `Read` | "What does getExpenses return?" | Reads `src/data/storage.js` |
| `Write` | "Create csvExport.js with an expenses-to-CSV function" | Creates the file from scratch |
| `Edit` | "In storage.js, add isDeleted: false to every new expense" | Changes just that line |
| `Bash` | "Install recharts and use it in ReportsPage" | Runs `npm install recharts`, then edits the file |
| `Search` | "Find every getExpenses() call that's missing an isDeleted filter" | Searches all files, lists each location |
| `WebSearch` | "What's the correct recharts BarChart API?" | Searches the web for current docs |
| `TodoWrite` | "Break ReportsPage into steps" | Creates a subtask checklist and works through it |
| `Task` | "Build the balance utils and UI in parallel" | Spawns two subagents working simultaneously |

---

## 11. Bash Mode

📖 **Docs:** https://code.claude.com/docs/en/tools-reference#bash-tool

> **What you'll learn:** Understand how Claude runs terminal commands, how to trigger it, and see real bash examples on the Splitmate project before learning how to control it in the next lecture (Permissions).
>
> **What you'll build:** No new feature — practical bash prompts on the existing Splitmate codebase.


### What Is Bash Mode?

Bash Mode is how Claude Code runs terminal commands. When Claude needs to install a package, check git status, run a build, or search for a pattern — it uses the Bash tool.

You don't switch to a separate "mode". Claude decides when to use Bash based on what you ask. If you say "install recharts", Claude uses Bash to run `npm install recharts`. If you say "show me the bundle size", Claude runs `npm run build` and reads the output.

---

### How to Trigger It

**Let Claude decide (most common):**

Just describe what you want — Claude picks up that it needs Bash:

```
Install recharts and confirm it's in package.json
```

```
Run the dev server and tell me if there are any startup errors
```

---

**Run a command directly yourself using `!`:**

Prefix any command with `!` to run it immediately without involving Claude:

```
!git status
!npm install
!cat .claude/bash-history.log
```

The `!` is for *you* — it bypasses Claude entirely and runs straight to the terminal. Claude doesn't see the output. Use it when you want to check something quickly.

---

**Ask Claude to run a specific command:**

```
Run git log --oneline -5 and summarise what changed in the last 5 commits
```

```
Run npm run build — if it fails, fix the errors and run it again
```

The difference from `!`: Claude runs the command, reads the output, and uses it in its response.

---

### Bash Mode vs Shell Commands in Slash Command Files

When you write `run git diff --name-only HEAD~1` inside a `.claude/commands/` file, Claude uses its Bash tool to execute it — same mechanism, different trigger. In a slash command file you're writing instructions for Claude. With `!` you're running the command yourself directly.

---

### Real Examples on Splitmate

**Check what files changed since the last commit:**

```
Show me what files changed since the last commit and give me a summary of what was done
```

Claude runs `git diff --name-only HEAD~1`, reads the file list, reads each file, and summarises the changes.

---

**Install a new dependency:**

```
Install recharts — we need it for the Reports page. Confirm it's installed and show the version.
```

Claude runs `npm install recharts`, checks `package.json`, and confirms the version.

---

**Check the build:**

```
Run npm run build and tell me:
- Did it succeed?
- What's the total JS bundle size?
- Any warnings I should care about?
```

---

**Find a pattern across the codebase:**

```
Search for every place we call getExpenses() and check if it filters
isDeleted === false before using the result. List any that don't.
```

Claude uses grep or the Search tool, reads each match, and flags the missing filters.

---

**Git commit with a proper message:**

```
Stage all changed files and create a commit.
Write a conventional commit message based on what we just built.
```

Claude runs `git add .`, writes a message like `feat: add unequal split toggle to AddExpenseModal`, and commits.

---

**Check for leftover console.log statements:**

```
Search the entire src/ folder for any console.log statements left in the code.
List each one with its file and line number.
```

---

### What Bash Mode Can't Do

- It can't run commands that need interactive input (e.g. `sudo` password prompts)
- It can't open browser windows or GUIs
- It can't persist environment changes across sessions (each Bash call is a fresh subshell)

---

### The Approval Prompt

The first time Claude wants to run a new command, it shows:

```
◆ Claude wants to run: npm install recharts
 [a] Approve once [A] Approve always [d] Deny [D] Deny always
```

Press `A` to allow `npm install` for the whole session — you won't be asked again. This is what the next lecture (Permissions §11) is about: how to pre-configure these rules so Claude doesn't have to ask at all.

---

## 12. Permissions & Allowed Tools

📖 **Docs:** https://code.claude.com/docs/en/permissions

> **What you'll learn:** You've now seen Claude run bash commands (§7) and the approval prompt appear. This lecture explains the permission system behind it — and how to configure rules so Claude can work without interrupting you for every command.
>
> **What you'll build:** No new feature — configures .claude/settings.json permissions for Splitmate.


### Why Permissions Exist

Claude Code can read files, write files, run terminal commands, and install packages. That's powerful — and it means you need a way to stay in control of what it actually does.

Permissions are the answer. By default, Claude Code asks before doing anything that could change your system. You decide in the moment — or set rules in advance so it doesn't have to ask.

---

### The Permission System

Not all actions are equally risky. Claude Code treats them differently:

| What Claude wants to do | Example | Does it ask? |
|---|---|---|
| Read files or run safe commands | Reading `storage.js`, running `git status`, `ls` | **No** — runs automatically |
| Run a bash command | `npm install recharts`, `git commit` | **Yes** — asks first |
| Edit or write a file | Creating a component, editing `App.jsx` | **Yes** — asks first |

Safe read-only commands like `ls`, `cat`, `grep`, `git status`, `git log` always run without prompting — you never get interrupted for those.

---

### Manage Permissions — The Prompt

When Claude needs to do something that requires approval, you see this in the terminal:

```
◆ Claude wants to run: npm install recharts
 [a] Approve once [A] Approve always [d] Deny [D] Deny always
```

**`a` — Approve once:** Allow it this time. Claude will ask again next time it wants to run this command.

**`A` — Approve always:** Allow it for the rest of this session without asking again. Claude Code also saves this as a rule in `settings.json` so future sessions remember it too.

**`d` — Deny once:** Block it this time. Claude will try to find another approach.

**`D` — Deny always:** Never allow this. Saved as a permanent deny rule.

In practice, you'll press `a` or `A` most of the time and barely notice permissions are happening.

**To see all your current rules at any point:**

```bash
/permissions
```

This shows every active rule and which file it came from.

---

### Allow, Ask, Deny Rules

Behind the prompt, there are three types of rules. You can set these in `.claude/settings.json` so Claude doesn't have to ask for common actions at all.

| Rule type | What it does |
|---|---|
| `allow` | Claude does this automatically — no prompt |
| `ask` | Claude always prompts, even if an allow rule exists |
| `deny` | Claude cannot do this at all |

Rules are checked in order: **deny → ask → allow**. Deny always wins.

---

### Setting Up Rules for Splitmate

Here's a practical `settings.json` for working on Splitmate:

```json
{
 "permissions": {
 "allow": [
 "Bash(npm *)",
 "Bash(git status)",
 "Bash(git log *)",
 "Bash(git diff *)",
 "Bash(git add *)",
 "Bash(git commit *)"
 ],
 "deny": [
 "Bash(git push *)",
 "Bash(rm -rf *)",
 "Bash(sudo *)"
 ]
 }
}
```

What this means in plain English:

- `"Bash(npm *)"` — Claude can run any npm command (install, build, test) without asking
- `"Bash(git commit *)"` — Claude can commit code freely
- `"Bash(git push *)"` — DENIED: you always push to GitHub yourself
- `"Bash(rm -rf *)"` — DENIED: no deleting folders without you confirming manually
- `"Bash(sudo *)"` — DENIED: no admin commands

The `*` is a wildcard — it means "anything after this". So `"Bash(npm *)"` matches `npm install`, `npm run dev`, `npm test`, and anything else starting with `npm`.

---

### Permission Modes

Instead of writing individual rules, you can set a mode that controls how Claude handles approvals across the whole session.

| Mode | What it means in practice |
|---|---|
| `default` | Standard — Claude asks before each new type of action |
| `acceptEdits` | Claude edits files freely, still asks for bash commands |
| `plan` | Claude can only read and analyse — cannot change anything |
| `bypassPermissions` | Skips all prompts — for automated scripts only, not daily use |

**Set it in `settings.json`:**

```json
{
 "defaultMode": "acceptEdits"
}
```

**Or when starting a session:**

```bash
claude --defaultMode plan
```

---

### Three Situations You'll Actually Use

**Building Splitmate day to day:**

Use `acceptEdits` so Claude edits files freely, with deny rules blocking anything destructive:

```json
{
 "defaultMode": "acceptEdits",
 "permissions": {
 "deny": ["Bash(git push *)", "Bash(rm -rf *)"]
 }
}
```

---

**Doing a code review — no changes allowed:**

```bash
claude --defaultMode plan
```

Then ask:

```
Review all the components in src/ and find anywhere we're showing
expenses without filtering out deleted ones. Don't change any files.
```

Claude reads everything, finds the issues, reports them — but cannot touch a single file.

---

**Running automated checks:**

```bash
claude --defaultMode bypassPermissions -p "Run the test suite and report any failures"
```

Only use `bypassPermissions` in a controlled environment like a CI pipeline or Docker container — not on your own machine during normal development.

---

## 13. Context — Add Files / Images / Assets

📖 **Docs:** https://code.claude.com/docs/en/context

> **What you'll learn:** Learn to use @file references, paste images, and give Claude exactly the context it needs to be accurate — not more, not less.
>
> **What you'll build:** Expense notes field added (using @file references). Debugging by pasting real browser errors demonstrated.


### What Is Context?
Context is everything Claude can "see" when forming a response — conversation history, files it has read, and anything you explicitly share. You control what it sees.

### Adding Files with `@`

```bash
# Reference a specific file:
Look at @src/utils/balance.js and add handling for isDeleted expenses

# Reference multiple files:
Compare @src/pages/CreateGroupPage.jsx and @src/components/AddExpenseModal.jsx
— extract shared form logic into a reusable hook

# Reference config:
Check @tailwind.config.js — why aren't my custom colors working?

# Reference the router:
Look at @src/App.jsx and add the /reports route
```

### Adding a Logo to Nav and Footer

Drop the logo file into `public/logo.svg` (files in `public/` are served at root — reference as `/logo.svg`, no import needed).

Then:
```
I've added logo.svg to the public/ folder. Add it to the Navbar (left side, links to /)
and Footer (centered). Create both components if they don't exist.
```

### Adding Images for Debugging / Design

```bash
# UI design mockup:
Here's the mockup for the GroupDetail page: [drag PNG]
Match this layout using Tailwind

# Screenshot a bug:
This is what the balance summary looks like on mobile: [drag screenshot]
Fix the layout so the amounts don't overflow

# Browser console error:
The console shows this error: [drag screenshot]
What's causing it and how do I fix it?
```

### Adding Pasted Content

```bash
# Paste a localStorage snapshot to debug:
Here's what's currently in localStorage:
{ "groups": [...], "expenses": [...] }
Why isn't the balance showing correctly?

# Paste an error:
Getting this in the console:
"Cannot read properties of undefined (reading 'map')"
at ExpenseList.jsx:24
Fix it.
```

### Smart Context Loading

```
✅ Use @file to point at what's relevant:
 "Look at @src/data/storage.js and add a deleteGroup function"

✅ Use constraints to keep scope tight:
 "Fix the balance bug in the settlement logic — don't change any other behaviour"

❌ Avoid broad sweeps that burn context budget:
 "Look through the whole project and tell me what's wrong"
```

---

### Building Splitmate — Using Context Effectively

These examples show the difference between vague prompts and context-rich ones. The more Claude knows about what already exists, the less it guesses.

---

**Feature: Add notes to an expense**

Without `@` references, Claude might add a `notes` field inconsistently with how storage.js works. With them, it reads the actual data shape first.

```
Look at @src/data/storage.js to see how expenses are currently structured.
Look at @src/components/AddExpenseModal.jsx to see the current form fields.

Add an optional "Notes" text field to the Add Expense modal.
Store it in the expense object as a `notes` string (empty string if not filled in).
Display it below the description on the group detail page if it's not empty.
```

One `@` file for the data model, one for the form. Claude reads both and integrates correctly.

---

**Debugging: Fix a real error by pasting it**

This is the most practical use of context. Instead of describing a bug, paste the actual error.

Build something, run it, then when something breaks — paste the error directly:

```
Getting this in the console when I open the group detail page:
TypeError: Cannot read properties of undefined (reading 'map')
 at GroupDetailPage.jsx:47

Here's what the group object looks like in localStorage right now:
{ "id": "abc", "name": "Goa Trip", "members": [...] }
— there's no "expenses" key on the group object.

Fix it.
```

Real error + real data = Claude diagnoses and fixes the actual problem, not a hypothetical one.

---

## 14. Context Window & Management

📖 **Docs:** https://code.claude.com/docs/en/context-management

> **What you'll learn:** Understand how the context window works, when to use /compact vs /clear, and how to see exactly what a prompt costs in tokens.
>
> **What you'll build:** Expense categories feature built — used as a live example to show token consumption, /status, /cost, /compact, and /clear in action.


### What Is the Context Window?
The context window is Claude's working memory — the total text (conversation + code + files) it can hold at once. When it fills up, Claude loses earlier parts of the conversation.

**Sonnet 4.6 / Opus 4.7:** Up to 1 million tokens

---

### Live Demo — See What a Feature Costs

Rather than talking about token costs abstractly, let's measure a real one.

**Step 1 — Check the baseline before building**

```
/status
```

Note the current token count. This is your starting point.

---

**Step 2 — Build Expense Categories**

Run this prompt (the same one from §09 Context, with three `@` file references):

```
Look at @src/data/storage.js to see how expenses are structured.
Look at @src/components/AddExpenseModal.jsx to see the current form.
Look at @src/pages/GroupDetailPage.jsx to see how expenses are displayed.

Add a Category field to the Add Expense modal with these options:
Food & Drinks, Transport, Accommodation, Activities, Shopping, Utilities, Other.
Default to "Other". Store as a category string on the expense.
Show a small category tag on each expense row in Group Detail.
```

Claude reads three files, generates changes across all three, and wires everything up.

---

**Step 3 — Check the cost immediately after**

```
/status
```

The token count jumped. The difference is exactly what that one prompt cost — reading three files, thinking through the changes, writing the code.

```
/cost
```

Shows the total session cost in dollars so far.

This is what "context" means in practice. Every file you `@` reference, every line Claude reads and writes — it all goes into the window and adds to the count.

---

### Signs You Need to Manage Context

- Claude "forgets" what you built earlier in the session
- Responses contradict decisions you already made
- Claude asks for information you already provided
- `/status` shows context is more than 70% full

---

### The Three Commands

```bash
/status
# See current token count and how full the window is
# Run before and after big tasks to see what they cost

/compact
# Claude summarises the conversation into compressed memory
# Frees up space without losing progress
# Use when: mid-feature, context is filling up

/clear
# Wipes conversation history — Claude starts completely fresh
# Use when: switching to a completely new feature or topic
```

---

### Demo — /compact vs /clear

After building Expense Categories, demonstrate the difference:

**Run /compact:**

```
/compact
```

Then ask:

```
What category options did we just add?
```

Claude answers correctly — the summary preserved it.

**Now run /clear:**

```
/clear
```

Ask the same question:

```
What category options did we just add?
```

Claude has no idea. The slate is completely wiped.

That's the difference. `/compact` preserves a summary. `/clear` starts fresh.

---

### AutoMemory — How Claude Remembers Between Sessions

AutoMemory automatically saves important context from your session to `CLAUDE.md` before it gets cleared — so the next session starts with your decisions already loaded.

**Enabling AutoMemory:**

```json
{
 "memory": {
 "autoSave": true,
 "target": "CLAUDE.md",
 "saveOn": ["clear", "compact", "session_end"]
 }
}
```

**The flow:**

```
Session 1:
 Build Expense Categories → establish category list
 /clear → AutoMemory saves to CLAUDE.md

Session 2:
 Claude reads CLAUDE.md → already knows about categories
 No re-explaining needed → build the next feature immediately
```

---

### Context Strategy for Splitmate

```
Starting a new feature:
 /clear → AutoMemory saves session to CLAUDE.md
 Claude auto-reads CLAUDE.md
 @mention only the files relevant to this feature

Mid-feature, context getting full:
 /compact → continue working without losing progress

End of session:
 /update-claude-md → update project memory
 /clear
```

---

### Good Habits

```
✅ Run /status before and after big prompts — see what they cost
✅ Use @file instead of "look through the whole project"
✅ /compact when switching between sub-tasks within a feature
✅ /clear when moving to a completely different feature
✅ AutoMemory enabled — CLAUDE.md stays up to date automatically
❌ Don't paste entire log files — paste only the relevant error lines
❌ Don't ignore /status until things start breaking
```

---

## 15. Hooks

📖 **Docs:** https://code.claude.com/docs/en/hooks

> **What you'll learn:** Set up hooks that run automatically before or after Claude uses a tool — auto-formatting, bash logging, and blocking destructive commands.
>
> **What you'll build:** Dev tooling only — three hooks configured in .claude/settings.json: auto-Prettier on write, bash command logger, destructive command blocker.


### What Are Hooks?

Hooks are **scripts that Claude Code automatically runs** at specific moments — before a tool fires, after it completes, when a notification appears, or when Claude finishes responding. You define them once in `.claude/settings.json` and they run silently in the background every session.

Think of hooks as invisible team members: one always formats your code, one keeps a log of everything, one blocks you from making mistakes. You never have to ask.

---

### Hook Events — What's Available

| Event | When It Fires | `matcher` | Common use |
|---|---|---|---|
| `PreToolUse` | Before Claude uses any tool | ✅ Yes | Block dangerous commands, log before action |
| `PostToolUse` | After Claude uses any tool | ✅ Yes | Auto-format files, run linter, trigger tests |
| `Notification` | When Claude sends a desktop notification | ❌ No | Forward to Slack, log alerts, filter noise |
| `Stop` | When Claude finishes its entire response | ❌ No | Auto-update CLAUDE.md, send a summary, cleanup |

`Notification` and `Stop` have no `matcher` — they always fire when the event occurs.

---

### Hook Events in Detail

**`PreToolUse`** — runs before any tool call. If your script exits with code 1, the tool call is **cancelled entirely**. Claude sees the error output and explains why it was blocked.

```
PreToolUse → script runs → exit 0 → tool continues as normal
 → exit 1 → tool is CANCELLED, Claude explains why
```

Best for: blocking dangerous bash commands, logging what's about to happen, enforcing rules before Claude touches anything.

---

**`PostToolUse`** — runs after a tool completes. Cannot cancel the tool (it already ran), but can trigger follow-up actions. The `$RESULT` variable contains the tool's output.

```
Tool completes → PostToolUse fires → script runs
```

Best for: auto-formatting files Claude just wrote, running the linter after an edit, checking the build still passes.

---

**`Notification`** — fires whenever Claude Code sends you a desktop notification (e.g. "Waiting for your input", "Task complete"). Your script receives the notification text via `$NOTIFICATION`.

```
Claude sends a notification → script runs with $NOTIFICATION set
```

Best for: forwarding to Slack or your phone, logging when Claude needs attention, suppressing noisy alerts.

Example command:

```bash
curl -s -X POST $SLACK_WEBHOOK -d "{\"text\":\"Claude: $NOTIFICATION\"}" > /dev/null
```

---

**`Stop`** — fires once when Claude finishes its entire response. Ideal for end-of-session automation that should happen after every conversation turn.

```
Claude finishes responding → Stop fires → script runs
```

Best for: appending a session summary to a log, reminding yourself to run `/update-claude-md`, sending a "Claude is done" desktop notification.

Example command:

```bash
echo "[$(date '+%H:%M:%S')] Session ended" >> .claude/sessions.log
```

---

### What Is `matcher`?

The `matcher` field is a **regex pattern** that filters which tools trigger your `PreToolUse` or `PostToolUse` hook. Without it, the hook fires on every tool use.

```
"matcher": "Write" → only when Claude creates a new file
"matcher": "Edit" → only when Claude edits a file
"matcher": "Write|Edit" → Write or Edit (| means OR in regex)
"matcher": "Bash" → only terminal commands
"matcher": "Read" → only file reads
"matcher": ".*" → every tool (use sparingly)
```

Available tool names to match: `Read` `Write` `Edit` `Bash` `Search` `WebSearch` `WebFetch` `Task`

---

### Available Variables in Hook Commands

```bash
$FILE_PATH # path of file being read/written/edited
$COMMAND # bash command about to run (Bash hooks)
$TOOL_NAME # name of the tool being used
$RESULT # output of the tool — PostToolUse only
$NOTIFICATION # notification text — Notification event only
```

---

### Configuring Hooks

All hooks live in `.claude/settings.json`. Each event can have multiple matcher groups, and each matcher group can have multiple hook commands — they run in order.

```json
{
 "hooks": {
 "PreToolUse": [
 {
 "matcher": "Bash",
 "hooks": [
 { "type": "command", "command": "your-pre-bash-script" }
 ]
 }
 ],
 "PostToolUse": [
 {
 "matcher": "Write|Edit",
 "hooks": [
 { "type": "command", "command": "your-post-write-script" }
 ]
 }
 ],
 "Stop": [
 {
 "hooks": [
 { "type": "command", "command": "your-stop-script" }
 ]
 }
 ]
 }
}
```

---

### Hook 1: Auto-format every file Claude writes

**Event:** `PostToolUse` · **Matcher:** `Write|Edit`

Every time Claude creates or edits a file, Prettier runs on it immediately. You always get consistently formatted code regardless of how Claude wrote it.

```json
{
 "hooks": {
 "PostToolUse": [
 {
 "matcher": "Write|Edit",
 "hooks": [
 {
 "type": "command",
 "command": "npx prettier --write '$FILE_PATH' 2>/dev/null || true"
 }
 ]
 }
 ]
 }
}
```

**Demo:**

Install Prettier if not already:

```bash
npm install -D prettier
```

Ask Claude to write messy code on purpose:

```
Create a React component called TestCard in src/components/TestCard.jsx.
Write it with inconsistent indentation — don't clean it up.
```

Watch the terminal: Claude writes the file, then the hook fires and Prettier cleans it immediately. Open `TestCard.jsx` — it is perfectly formatted even though Claude wrote it messily.

---

### Hook 2: Log every bash command Claude runs

**Event:** `PreToolUse` · **Matcher:** `Bash`

Every terminal command Claude runs gets timestamped in a log file. After a session you can see exactly what Claude did and when.

```json
{
 "hooks": {
 "PreToolUse": [
 {
 "matcher": "Bash",
 "hooks": [
 {
 "type": "command",
 "command": "echo \"[$(date '+%Y-%m-%d %H:%M:%S')] $COMMAND\" >> .claude/bash-history.log"
 }
 ]
 }
 ]
 }
}
```

**Demo:**

Create the log directory first:

```bash
mkdir -p .claude
```

Give Claude a task that involves several commands:

```
Install the date-fns package, check the current git status, then run the dev server.
```

When Claude finishes, read the log:

```bash
cat .claude/bash-history.log
```

You will see:

```
[2026-04-18 14:23:01] npm install date-fns
[2026-04-18 14:23:09] git status
[2026-04-18 14:23:12] npm run dev
```

Every command is there, in order, with exact timestamps.

---

### Hook 4: Block destructive bash commands

**Event:** `PreToolUse` · **Matcher:** `Bash` · **Exit 1 cancels the tool**

If Claude tries to run a dangerous command, the hook exits with code 1 and Claude Code cancels the command entirely before it runs.

```json
{
 "hooks": {
 "PreToolUse": [
 {
 "matcher": "Bash",
 "hooks": [
 {
 "type": "command",
 "command": "if echo '$COMMAND' | grep -qE 'rm -rf [^n]|DROP TABLE|git reset --hard'; then echo 'BLOCKED: run this manually if you are sure' && exit 1; fi"
 }
 ]
 }
 ]
 }
}
```

**Demo — trigger the block:**

```
Delete everything in the src/components/ folder — we are going to rebuild it from scratch.
```

Claude attempts `rm -rf src/components/`. The hook fires, prints `BLOCKED: run this manually if you are sure`, exits 1, and Claude Code cancels the command. Claude responds explaining what happened and asks you to confirm manually.

**Demo — show it allows safe commands:**

```
Clean up the node_modules folder and do a fresh install.
```

Claude runs `rm -rf node_modules` — the hook checks the pattern. `[^n]` means "not followed by the letter n", so `node_modules` passes right through. Only paths that start with something other than `n` are blocked.

---

### Complete `settings.json` — All Three Hooks Together

```json
{
 "hooks": {
 "PreToolUse": [
 {
 "matcher": "Bash",
 "hooks": [
 {
 "type": "command",
 "command": "echo \"[$(date '+%H:%M:%S')] $COMMAND\" >> .claude/bash-history.log"
 },
 {
 "type": "command",
 "command": "if echo '$COMMAND' | grep -qE 'rm -rf [^n]|DROP TABLE|git reset --hard'; then echo 'BLOCKED' && exit 1; fi"
 }
 ]
 }
 ],
 "PostToolUse": [
 {
 "matcher": "Write|Edit",
 "hooks": [
 {
 "type": "command",
 "command": "npx prettier --write '$FILE_PATH' 2>/dev/null || true"
 }
 ]
 }
 ]
 }
}
```

When multiple hooks share a matcher (the two `Bash` hooks above), they run in order. The logger always runs first — so even if the blocker cancels a command, you still have a record that it was attempted.

---

### What Else Could We Hook Into? (For Discussion)

The three hooks we implemented cover the most common use cases. But Claude Code exposes many more events — here's what they could do in a project like Splitmate. These are **not implemented**, just worth knowing.

| Event | When it fires | Splitmate use case |
|---|---|---|
| `SessionStart` | When you open a Claude Code session | Print the CLAUDE.md status checklist at the start of every session so you know what's left to build |
| `SessionEnd` | When the session closes (`/clear`, logout, exit) | Auto-run `/update-claude-md` so CLAUDE.md always reflects what was built, without remembering to do it |
| `SubagentStart` | When a subagent is spawned | Log which agents launched during §19 parallel builds, or inject "read CLAUDE.md before starting" into every subagent automatically |
| `SubagentStop` | When a subagent finishes | Track how long each agent took — useful in the 4-agent export history team to spot bottlenecks |
| `PreCompact` | Before `/compact` runs | Save a snapshot of the full conversation before it gets compressed |
| `PostCompact` | After `/compact` runs | Log that compaction happened and how much context was freed |
| `FileChanged` | When a specific file changes on disk | Watch `src/data/storage.js` — warn if it's edited directly, since nothing outside storage.js should touch localStorage |
| `StopFailure` | When Claude's response fails (rate limit, server error) | Send a desktop notification mid-session so you don't come back to a silently stalled Supabase migration |
| `ConfigChange` | When `.claude/settings.json` is modified | Log when hooks config changes — useful safety net in a team project |
| `UserPromptSubmit` | Every time you send a message | Log every prompt to a file — a personal record of everything you've asked Claude Code to do |

📖 Full list: https://code.claude.com/docs/en/hooks

---

## 16. MCP (Model Context Protocol)

📖 **Docs:** https://code.claude.com/docs/en/mcp | **Supabase MCP:** https://supabase.com/docs/guides/getting-started/mcp

> **What you'll learn:** Connect Claude to external services via MCP — and use it to migrate Splitmate from localStorage to Supabase.
>
> **What you'll build:** Phase 2 migration — Supabase database created, app updated to use real authentication and cloud storage instead of localStorage.


### What Is MCP?

So far in this course, Claude Code has only worked with files on your computer. It reads your code, writes your code, and runs terminal commands — but everything lives locally.

**MCP changes that.** It's a standard that lets Claude connect to external services — databases, APIs, tools — and interact with them directly.

The clearest example: instead of Claude writing code that *talks to* Supabase, Claude actually *connects to* Supabase and creates the tables, sets up the rules, and seeds the data itself — in real time, while you watch.

```
Without MCP:
 You → Claude → "here's the code to create this table" → you run it manually

With MCP:
 You → Claude → Claude creates the table directly in Supabase
```

---

### Phase 2 — Moving from localStorage to Supabase

In Phase 1, all data lives in the browser's localStorage. This means two people on different devices can't share data — every "user" you tested was just you, in the same browser.

Phase 2 moves everything to Supabase — a real database with real authentication. Shubham and Bob can now use the app on different devices and actually see each other's groups.

The migration has 9 steps. Most are just prompts you send to Claude.

---

### The 9 Steps

---

**Step 1 — Connect Claude to Supabase**

> **You do this** — add one block to `.claude/settings.json`:

```json
{
 "mcpServers": {
 "supabase": {
 "type": "http",
 "url": "https://mcp.supabase.com/mcp?project_ref=your-project-ref"
 }
 }
}
```

Replace `your-project-ref` with your Supabase project's reference ID (found in Project Settings → General).

> **Then** — Claude Code opens your browser automatically. Sign in to Supabase and click Allow.

That's it. Claude can now talk directly to your database.

---

**Step 2 — Confirm it's working**

```
What tables are in the Supabase database right now?
```

Claude lists your tables (probably empty at this point) or says "no tables yet". Either way — the connection works.

---

**Step 3 — Turn off email confirmation**

> **You do this in the Supabase Dashboard:**
> Authentication → Providers → Email → Turn off "Confirm email"

This means when you register a test account, you don't need to check an inbox first. Good for development.

Then confirm it worked:

```
Show me the current auth settings in Supabase.
```

---

**Step 4 — Create the database and set up security**

Supabase Auth already manages users — we don't need a separate `users` table or a trigger to link them. We just need tables for the app data and RLS to keep everything private.

```
Look at @src/data/storage.js — it has all the data structures we use.

Using the Supabase MCP:
- Create matching tables for the app data
- Add foreign keys and indexes
- Enable Row Level Security on all tables
- Add RLS policies so users can only see data from groups they belong to

Show me the tables and policies after creating them.
```

Claude reads the file, understands the data shapes, and creates everything directly in Supabase.

---

**Step 7 — Add test data**

```
Add realistic test data to the Supabase database for Splitmate.

Create at least 3 users, 2 groups with different members, and several expenses
spread across both groups with different people paying. Include one pending
member who hasn't registered yet.

Make the data realistic — real expense descriptions, varied amounts.
Show me the row counts when done.
```

---

**Step 8 — Create the .env file**

Before the app can talk to Supabase, it needs your project's credentials. This is completely separate from the MCP connection — MCP is how Claude Code talks to Supabase during development. The `.env` file is how your React app talks to Supabase at runtime.

> **You do this manually:**
>
> Supabase Dashboard → Project Settings → API Keys
>
> Copy **Project URL** and **Publishable key** (`sb_publishable_...`). Create a `.env` file in the project root:

```
VITE_SUPABASE_URL=https://your-project-ref.supabase.co
VITE_SUPABASE_PUBLISHABLE_KEY=sb_publishable_...
```

> The publishable key replaces the legacy anon key — it's safe to use in frontend code and is protected by the RLS rules we set up in Step 4.
>
> Add `.env` to `.gitignore` so credentials aren't committed:

```bash
echo ".env" >> .gitignore
```

---

**Step 9 — Update the login and registration**

The app currently uses localStorage for auth. Now it uses Supabase Auth — which handles passwords securely, sessions properly, and works across devices.

```
Update Splitmate to use Supabase Auth instead of localStorage.
Keep the same useAuth() hook — nothing else in the app should change.
The app should remember the session if the browser is closed and reopened.
```

> **After Claude finishes, verify in the browser:**
> - Register a new account — confirm it works without checking email
> - Log out and log back in — confirm it remembers you

---

**Step 10 — Update the data layer**

The last step — switch storage.js from reading localStorage to querying Supabase.

```
Update storage.js so all data comes from Supabase instead of localStorage.

Keep all the function names the same — getGroups(), getExpenses() etc.
No other file in the app should need to change.

Important:
- getGroups() should only return groups the current user belongs to
- getExpenses() should never return deleted expenses
- Members can be active or pending — include both, the UI handles the display
- Remove all localStorage code
```

> **After Claude finishes, verify in the browser:**
> - Log in and confirm groups and expenses load correctly
> - Add a new expense — confirm it saves and appears after refresh
> - Open a second browser profile, log in as a different user — confirm they only see their own groups

---

### What Else Can MCP Connect To?

Supabase is just one example. The same pattern works for:

```
GitHub → Claude reads issues, creates PRs, checks CI status
Slack → Claude posts messages, reads channels
Figma → Claude reads design tokens directly
Stripe → Claude checks payments, creates products
Vercel → Claude deploys, checks build logs
```

Any service with an MCP server can be wired up the same way — one block in settings.json and Claude can interact with it directly.

---


## 17. Skills

📖 **Docs:** https://code.claude.com/docs/en/skills

> **What you'll learn:** Create reusable skill directories that give Claude consistent templates and patterns — and see how a skill changes Claude's behaviour on the same question.
>
> **What you'll build:** Four skills: explain-code (personal, demo), react-component (project, templates), accessibility (project, audit + patterns), impeccable (community, UI design polish — installed via npx).


### What Are Skills?
A skill is a **directory** inside `.claude/skills/` that teaches Claude a specific behaviour, pattern, or template. It contains a `SKILL.md` instruction file plus any supporting material — templates, example files, checklists, and scripts.

Claude reads the skill in two ways:
- **Automatically** — when it detects a task that matches the skill's description
- **On demand** — when you invoke it as a slash command

**Every skill is also a slash command.** A skill named `explain-code` creates `/explain-code`. You can call it directly any time:

```
/explain-code src/utils/balance.js
```

Claude reads the `SKILL.md`, applies the instructions (analogy → diagram → walkthrough → gotcha), and explains that specific file. The skill's name in the frontmatter becomes the command.

This is the same system as Custom Slash Commands — skills and commands are now unified. A skill in `.claude/skills/explain-code/SKILL.md` and a command in `.claude/commands/explain-code.md` both create `/explain-code`. Skills are the recommended approach since they support supporting files and auto-detection.

**You can get skills two ways:**

- **Write your own** — tailored exactly to your project's conventions, data models, and rules. This is what the react-component and accessibility skills in this lecture are.
- **Use someone else's** — the community has built and shared skills for common problems. Install them in seconds with `npx skills add`. This is what Impeccable (Skill 4) is.

Both live in the same place and work the same way. The only difference is whether you wrote it or downloaded it.

Skills can live in two places:

| Location | Path | Available in |
|---|---|---|
| Personal | `~/.claude/skills/<name>/SKILL.md` | All your projects |
| Project | `.claude/skills/<name>/SKILL.md` | This project only |

```
~/.claude/skills/ ← personal (all projects)
└── explain-code/
 └── SKILL.md → creates /explain-code

.claude/skills/ ← project (Splitmate only)
├── react-component/
│ ├── SKILL.md → creates /react-component
│ ├── template.jsx
│ └── component-examples.md
├── accessibility/
│ ├── SKILL.md → creates /accessibility
│ ├── audit-checklist.md
│ └── bad-vs-good.md
└── impeccable/ ← installed via npx skills add
 └── SKILL.md → creates /impeccable
```

### Skills vs. CLAUDE.md vs. Custom Commands

| | CLAUDE.md | Skills |
|---|---|---|
| Always active? | ✅ Yes | ✅ Auto-detected + `/command` on demand |
| Purpose | Project memory | Behaviour, templates, patterns |
| Invocation | Automatic | Auto or `/skill-name` |
| Contains | Text conventions | SKILL.md + supporting files |

---

### Skill 1: `explain-code` (Personal skill — demo)

This is the example straight from the official docs. It's a **personal skill** — installed once, available in every project you work on.

The demo shows exactly what skills are for: ask the same question before and after installing the skill, and Claude's answer changes completely without you changing the question at all.

---

#### Step 1 — Ask without the skill first

```
How does the balance calculation in src/utils/balance.js work?
```

Claude gives a competent but generic answer — reads the file and describes what it does. No particular structure, no diagram, no analogy. This is Claude's default behaviour.

---

#### Step 2 — Install the skill

Personal skills live in `~/.claude/skills/` — available across all your projects.

```bash
mkdir -p ~/.claude/skills/explain-code
```

Create `~/.claude/skills/explain-code/SKILL.md`:

```markdown
---
name: explain-code
description: Explains code with visual diagrams and analogies. Use when explaining how code works, teaching about a codebase, or when the user asks "how does this work?"
---

When explaining code, always include:

1. **Start with an analogy**: Compare the code to something from everyday life
2. **Draw a diagram**: Use ASCII art to show the flow, structure, or relationships
3. **Walk through the code**: Explain step-by-step what happens
4. **Highlight a gotcha**: What is a common mistake or misconception?

Keep explanations conversational. For complex concepts, use multiple analogies.
```

No restart needed — Claude Code detects the new skill automatically.

---

#### Step 3 — Ask the exact same question again

```
How does the balance calculation in src/utils/balance.js work?
```

Now Claude:

1. **Gives an analogy** — "Think of it like splitting a restaurant bill: everyone has paid for something, and at the end you figure out the minimum number of transfers to make everyone even."
2. **Draws an ASCII diagram** — shows money flowing between Shubham, Bob, and Rahul
3. **Walks through the code** — step by step: filter deleted expenses → calculate net per member → minimise transactions
4. **Flags the gotcha** — "If you forget to filter `isDeleted === false` first, deleted expenses are still counted and the balance is wrong."

Same question. Completely different answer. That's the skill.

---

#### Step 4 — Invoke it directly too

You can also invoke the skill explicitly with any file:

```
/explain-code src/context/AuthContext.jsx
```

```
/explain-code src/data/storage.js
```

Claude applies the same analogy + diagram + walkthrough + gotcha structure every time.

---

### Skill 2: `react-component`

**`.claude/skills/react-component/SKILL.md`**

```markdown
---
name: react-component
description: Creates React components following Splitmate's conventions.
when_to_use: Use when creating any new JSX component — pages, cards, forms, layouts.
---

# React Component Skill

## Where files go
- Reusable UI pieces → src/components/[ComponentName].jsx
- Full pages → src/pages/[PageName].jsx
- Primitive UI elements → src/components/ui/[Name].jsx

## Required structure
See template.jsx for the exact code structure.

## Rules
- Tailwind utility classes ONLY — no inline style={} and no .css files
- Mobile-first: write base classes for mobile, then sm: and md: for larger screens
- Every list component needs an EmptyState — never render nothing silently
- Every component that can be loading needs a loading state (spinner or skeleton)
- Props destructured at the top of the function
- No business logic in components — call hooks or utils
- No localStorage access — use storage.js through hooks
- Use the ui/ primitives: Button, Card, Badge, EmptyState, PageWrapper

## Styling reference
- Page background: bg-gray-50
- Card: bg-white rounded-xl shadow-sm border border-gray-100 p-4
- Heading: text-xl font-semibold text-gray-900
- Secondary text: text-sm text-gray-500
- Primary button: from Button.jsx (variant="primary") — never hardcode button styles
- Balance positive: text-green-600 font-medium
- Balance negative: text-red-600 font-medium
- Pending badge: <Badge color="amber">Pending</Badge>

## Component size limits
If a component exceeds ~150 lines, split it. The parent renders layout,
child components handle the individual pieces.
```

**`.claude/skills/react-component/template.jsx`**

```jsx
import { useState } from 'react'
import { Card, Button, Badge, EmptyState } from './ui'

/**
 * ComponentName — one-line description of what it renders.
 *
 * @param {Object} props
 * @param {string} props.title - The main label to display
 * @param {Function} props.onAction - Called when primary action is triggered
 */
function ComponentName({ title, items = [], onAction }) {
 // 1. Local state
 const [isLoading, setIsLoading] = useState(false)

 // 2. Derived values (computed from props — no useEffect needed)
 const hasItems = items.length > 0

 // 3. Event handlers
 function handleAction() {
 setIsLoading(true)
 onAction?.()
 }

 // 4. Loading state
 if (isLoading) {
 return (
 <Card>
 <div className="animate-pulse h-16 bg-gray-100 rounded" />
 </Card>
 )
 }

 // 5. Empty state (for list components)
 if (!hasItems) {
 return (
 <EmptyState
 message="Nothing here yet"
 actionLabel="Add one"
 onAction={handleAction}
 />
 )
 }

 // 6. Main render
 return (
 <Card>
 <h2 className="text-lg font-semibold text-gray-900 mb-3">{title}</h2>
 <ul className="space-y-2">
 {items.map(item => (
 <li key={item.id} className="text-sm text-gray-700">
 {item.name}
 </li>
 ))}
 </ul>
 <div className="mt-4">
 <Button variant="primary" onClick={handleAction}>
 Primary Action
 </Button>
 </div>
 </Card>
 )
}

export default ComponentName
```

**`.claude/skills/react-component/component-examples.md`**

```markdown
# Real Component Examples from Splitmate

These show how we apply the skill in practice.

## GroupCard (simple display card)
Props: group (object), currentUserId
Renders: Card with group name, member avatars, balance line
Key rules: avatar initials from name.slice(0,2).toUpperCase(), balance from useGroupBalances

## ExpenseCard (item in a list)
Props: expense (object), members (array), currentUserId
Renders: description, amount, paidByName, date, "X people" count
Key rules: never compute balance here — just display splits[]

## BalanceSummary (computed view)
Props: settlements (array from minimizeTransactions), currentMemberId
Renders: settlements split into "involves you" (prominent) and "others" (muted)
Key rules: find member name from group.members, format $ amounts with formatters.js

## StatCard (number with label)
Props: label, value, trend ("positive"|"negative"|"neutral")
Renders: large value, small label, color from trend
Used in: Dashboard top bar
```

---

### Skill 3: `accessibility`

**`.claude/skills/accessibility/SKILL.md`**

```markdown
---
name: accessibility
description: Audits and improves accessibility of React components.
when_to_use: Use after building any form, interactive component, or page. Run before marking a feature done.
---

# Accessibility Skill

## Goal
Every user should be able to use Splitmate with a keyboard only, with a screen reader,
or with low vision. Accessibility is not optional.

## Audit checklist
See audit-checklist.md for the full checklist.
See bad-vs-good.md for before/after examples.

## Priority order
1. Forms — most interaction happens in forms
2. Buttons and links — must be operable by keyboard
3. Error states — screen readers must announce errors
4. Headings — navigation landmark for screen reader users
5. Color — never the only indicator of meaning

## How to apply this skill
After building a component:
1. Run through audit-checklist.md
2. For each issue, apply the pattern from bad-vs-good.md
3. Test: tab through the component — does focus order make sense?
4. Test: does every interactive element have a visible focus ring?
```

**`.claude/skills/accessibility/audit-checklist.md`**

```markdown
# Accessibility Audit Checklist

Run this after building any component or page.

## Forms
- [ ] Every <input> has an <label> with matching htmlFor/id pair
- [ ] Required fields marked with required attribute AND visual indicator
- [ ] Error messages use role="alert" so screen readers announce them
- [ ] Error messages linked to field via aria-describedby="error-id"
- [ ] Placeholders exist alongside labels — never instead of labels
- [ ] submit button has descriptive text ("Save expense" not just "Submit")

## Buttons and links
- [ ] Every <button> has text or aria-label (icon-only buttons fail without it)
- [ ] No <div onClick> — use <button> for actions, <a> for navigation
- [ ] Disabled buttons have aria-disabled="true" (not just visually grayed)
- [ ] Focus ring visible on all interactive elements (use focus:ring-2 focus:ring-orange-500)

## Images and icons
- [ ] Decorative icons: aria-hidden="true"
- [ ] Meaningful images: descriptive alt text
- [ ] Icon-only buttons: aria-label on the button, aria-hidden on the icon

## Structure and navigation
- [ ] Page has exactly one <h1>
- [ ] Headings don't skip levels (h1 → h2 → h3, never h1 → h3)
- [ ] Lists use <ul>/<ol> + <li>, not nested <div>s
- [ ] Modals trap focus and restore it on close

## Color and contrast
- [ ] Balance positive/negative uses color AND text ("You're owed" / "You owe")
- [ ] Pending status uses color AND text ("Pending" label — not just gray dot alone)
- [ ] Don't rely on color alone anywhere
```

**`.claude/skills/accessibility/bad-vs-good.md`**

```markdown
# Accessibility Patterns — Bad vs. Good

## Form labels

BAD — placeholder disappears when the user starts typing:
 <input placeholder="Enter email" />

GOOD — label linked via htmlFor + id:
 <label htmlFor="email" className="block text-sm text-gray-700 mb-1">
 Email address
 </label>
 <input id="email" placeholder="shubham@test.com" className="..." />

---

## Error messages

BAD — screen readers don't announce a plain paragraph:
 {error && <p className="text-red-500">{error}</p>}

GOOD — role="alert" triggers announcement, aria-describedby links error to field:
 {error && (
 <p id="email-error" role="alert" className="text-sm text-red-600 mt-1">
 {error}
 </p>
 )}
 <input
 id="email"
 aria-describedby={error ? "email-error" : undefined}
 aria-invalid={!!error}
 />

---

## Icon-only buttons

BAD — screen reader says "button" with zero context:
 <button onClick={onDelete}><TrashIcon /></button>

GOOD — aria-label describes the action:
 <button onClick={onDelete} aria-label="Delete expense">
 <TrashIcon aria-hidden="true" />
 </button>

---

## Status indicators

BAD — color only, useless for colorblind users:
 <span className={isPending ? "text-amber-500" : "text-green-500"}>●</span>

GOOD — color AND text:
 <Badge color={isPending ? "amber" : "green"}>
 {isPending ? "Pending" : "Active"}
 </Badge>

---

## Balance amounts

BAD — a red number means nothing without sight:
 <span className={amount > 0 ? "text-green-600" : "text-red-600"}>
 ${Math.abs(amount)}
 </span>

GOOD — color AND label, text alone conveys the meaning:
 <span className={amount > 0 ? "text-green-600" : "text-red-600"}>
 {amount > 0 ? "You're owed" : "You owe"} ${Math.abs(amount)}
 </span>

---

## Clickable divs

BAD — divs aren't in the tab order, keyboard users can't reach them:
 <div onClick={() => navigate(`/group/${id}`)}>
 <h3>{group.name}</h3>
 </div>

GOOD — button is focusable and keyboard-operable by default:
 <button onClick={() => navigate(`/group/${id}`)} className="text-left w-full">
 <h3>{group.name}</h3>
 </button>
```

---

### Activating Skills

```
# explain-code — Claude detects it automatically:
"How does the balance calculation work?"
# → Claude sees this matches explain-code and loads it

# Or invoke directly with any file:
"/explain-code src/utils/balance.js"
"/explain-code src/context/AuthContext.jsx"

# react-component — Claude detects it automatically:
"Create a GroupCard component"
# → Claude sees this needs react-component skill and loads it

# Or reference it explicitly:
"Using the react-component skill, create a MonthlyGroupCard
that shows group name, this month's total, and a small balance indicator"

# accessibility skill — run it after any feature:
"Using the accessibility skill, audit AddExpenseModal
and fix every issue in the checklist"

# Or combine skills:
"Create an ExpenseSplitRow component (react-component skill),
then audit it with the accessibility skill"

# impeccable — run on the whole UI:
/audit
/polish
/critique dashboard
```

---

### Skill 4: `impeccable` (Community skill — UI design polish)

**GitHub:** https://github.com/pbakaus/impeccable · 19k+ stars · 16k+ installs

Impeccable is the most popular design skill in the Claude Code ecosystem. It was built by Paul Bakaus (creator of jQuery UI, former Google Developer Advocate) to solve a specific problem: AI-generated UIs all look the same — Inter font, purple gradients, nested cards, generic layouts. The community calls it "AI slop."

Impeccable gives Claude a shared design vocabulary so it stops defaulting to safe, generic choices.

---

#### What it does

It ships with 23 slash commands, each targeting a specific design failure:

| Command | What it does |
|---|---|
| `/audit` | Accessibility, performance, and responsive quality check |
| `/critique` | Deep UX review — hierarchy, clarity, visual weight |
| `/polish` | Final pass — alignment, spacing, micro-details |
| `/typeset` | Fix font choices, size hierarchy, and type scale |
| `/distill` | Strip complexity that doesn't earn its place |
| `/normalize` | Align everything with your design system |

It also has a CLI that detects anti-patterns before you even ask Claude — purple gradients, dark glows, side-tab borders, cramped padding, and 20+ more.

---

#### Installing Impeccable on Splitmate

**Step 1 — Install:**

```bash
npx skills add pbakaus/impeccable
```

Auto-detects Claude Code and puts the files in `.claude/skills/impeccable/`. No restart needed.

---

**Step 2 — Establish design context:**

```
/teach-impeccable
```

Claude asks you questions about your project: target audience, use cases, brand personality. It saves the answers to `.impeccable.md` at the project root. Every subsequent command uses this context automatically.

For Splitmate, you'd say something like:
- Target audience: young working professionals splitting expenses with friends
- Use case: groups, expenses, balances — used on mobile frequently
- Brand personality: clean, trustworthy, like Monzo — not a startup toy

---

**Step 3 — Run the example**

Open Splitmate in the browser. Take a screenshot of the current dashboard.

Then run:

```
/audit
```

Claude identifies specific issues across the whole UI — spacing inconsistencies, color contrast problems, typography hierarchy gaps, anything that looks auto-generated.

Then run:

```
/polish
```

Claude applies the final pass — tightens alignment, fixes spacing rhythm, cleans up micro-details.

Refresh the browser. Compare to the screenshot. The difference is the example.

---

**Step 4 — Target specific pages:**

```
/critique dashboard
/polish group-detail
/typeset
/audit add-expense-modal
```

Each command accepts an optional area argument so you can target exactly what you want to fix.

---

#### Why This Is Worth Showing in the Course

The before/after is visible in seconds — same app, same code structure, just visually better. It demonstrates that skills don't have to be things you write yourself. The community has already built tools for common problems, and one `npx` command is all it takes to benefit from that work.

---

## 18. Custom Slash Commands

📖 **Docs:** https://code.claude.com/docs/en/skills

> **What you'll learn:** Build workflow-focused skills that automate repeated development tasks — using the same skill system from §17, applied to project workflows.
>
> **What you'll build:** Four workflow skills created: /code-review, /accessibility-check, /readme, /update-claude-md. No new app feature.


### Skills as Workflow Automation

In §17 you created skills that teach Claude *how to think* — explain-code, react-component, accessibility patterns. This lecture uses the exact same system to automate *repeated workflows* — code review, README generation, CLAUDE.md updates.

Same mechanism. Different purpose.

| §17 Skills | §18 Custom Slash Commands |
|---|---|
| Teach Claude a behaviour or pattern | Automate a repeated workflow |
| `explain-code`, `react-component` | `code-review`, `readme` |
| Often auto-detected by Claude | Usually invoked manually |
| Can include templates and examples | Usually just instructions |

Both are skills. Both live in `.claude/skills/`. Both create a `/slash-command`.

---

### File Structure

Each workflow skill is a directory in `.claude/skills/` with a `SKILL.md` file:

```
.claude/skills/
├── code-review/
│ └── SKILL.md → /code-review
├── accessibility-check/
│ └── SKILL.md → /accessibility-check
├── readme/
│ └── SKILL.md → /readme
└── update-claude-md/
 └── SKILL.md → /update-claude-md
```

> **Note:** `.claude/commands/code-review.md` also works — the legacy commands format is still supported. But `.claude/skills/code-review/SKILL.md` is the recommended approach since it supports supporting files alongside the SKILL.md.

---

### Frontmatter

Every SKILL.md starts with a YAML frontmatter block between `---` markers:

```markdown
---
name: code-review
description: Review code for bugs, security issues, and Splitmate-specific rules
argument-hint: [file path]
---

Your instructions here...
```

| Field | What it does |
|---|---|
| `name` | The slash command name |
| `description` | Shows in `/help` — helps Claude auto-detect when to use it |
| `argument-hint` | Hint shown in autocomplete |

**Embedding bash output with `!`:** prefix a command with `!` to run it and insert the output directly into the prompt:

```markdown
## Context
Changed files: !`git diff --name-only HEAD~1`
```

Claude receives the actual file list — not instructions to run the command. This is how your skills can embed live git output without any special permission configuration.

---

That's the system. Now let's build four workflow skills one by one.

---

### Skill 1: `/code-review`

The code-review skill has three files — a main SKILL.md, a project-specific checklist, and a severity reference. This is the same pattern Impeccable uses: the main skill stays focused and delegates to supporting files for detailed rules.

```
.claude/skills/code-review/
├── SKILL.md → the main skill, creates /code-review
├── checklist.md → full rule set Claude reads during review
└── severity-guide.md → what ✅ ⚠️ ❌ mean and how to decide
```

---

#### `.claude/skills/code-review/SKILL.md`

```markdown
---
name: code-review
description: Review code for correctness, security, and Splitmate-specific data rules. Pass a file path to review that file only, or leave empty to review files from the last git commit.
argument-hint: [file path or empty]
---

# Code Review

You are a senior developer reviewing Splitmate — a multi-user expense sharing app.

Read @checklist.md — it has the full list of rules to check.
Read @severity-guide.md — it defines what each severity level means.

## Context
Last 3 commits: !`git log --oneline -3`

## Review scope

Argument received: "$ARGUMENTS"

- If it contains a file path → review that file only
- If it is empty → get the changed files: !`git diff --name-only HEAD~1`
 then review each of those files

## Output format

For each file reviewed, list every issue found:

 File: [filename]
 Line: [approximate line number]
 Severity: ✅ / ⚠️ / ❌
 Rule: [which rule from checklist.md]
 Issue: [what's wrong]
 Fix: [the corrected code or approach]

Group issues by file. After all files, print a final summary:

 Files reviewed: N
 ✅ Clean files: [list]
 ⚠️ Warnings: [count] — [brief list]
 ❌ Bugs: [count] — [brief list]

If zero issues found across all files: print "✅ All clear — no issues found."
```

---

#### `.claude/skills/code-review/checklist.md`

```markdown
# Code Review Checklist — Splitmate

## Correctness
- [ ] No function returns undefined when the caller expects a value
- [ ] .map() .filter() .find() only called on arrays that are guaranteed non-null
- [ ] Every async function call has await where needed
- [ ] useEffect dependency arrays are complete and correct
- [ ] No direct state mutation (always spread: {...obj, field: value})
- [ ] No array index used as React key — must use item.id

## Splitmate Data Rules ← violations here break balances silently
- [ ] Every expense query filters isDeleted === false before any logic
- [ ] Balance calculations filter deleted expenses first
- [ ] paidBy stores member.id (group-scoped) — NOT userId
- [ ] splits[].memberId stores member.id (group-scoped) — NOT userId
- [ ] balance.js reads splits[].amount directly — never divides expense.amount
- [ ] linkPendingMembers() is called on every registration path
- [ ] Dashboard filter: group.members.some(m => m.userId === user.id)

## Security
- [ ] Password field never appears in console.log, UI text, or component props
- [ ] localStorage never accessed directly outside src/data/storage.js
- [ ] No user input is rendered as raw HTML (dangerouslySetInnerHTML)
- [ ] No sensitive fields in URL params or query strings

## Code Quality
- [ ] No console.log left in production code
- [ ] No unused variables or imports
- [ ] No inline style={{ }} — Tailwind only
- [ ] No .then() chains — async/await only
- [ ] No hardcoded user IDs, group IDs, or test emails in production code
- [ ] All error states are handled (try/catch or .catch)

## React Conventions
- [ ] Components are functional (no class components)
- [ ] Hooks only called at the top level (not inside loops or conditions)
- [ ] useCallback / useMemo used where expensive operations are in render
- [ ] No prop drilling more than 2 levels deep — use context or lift state
```

---

#### `.claude/skills/code-review/severity-guide.md`

```markdown
# Severity Guide

## ❌ Bug — must fix before shipping
The code is wrong. It will cause incorrect behaviour, data corruption,
or a security issue in production. Examples:
- Expense balance showing wrong amount
- A user can see another user's groups
- Password logged to console
- isDeleted not filtered → deleted expenses included in balance

## ⚠️ Warning — should fix soon
The code works today but is fragile or inconsistent. Will likely cause
a bug when the codebase grows or requirements change. Examples:
- Array index as key (breaks on reorder)
- Missing error handling (silent failure)
- Inline style instead of Tailwind (hard to maintain)
- useEffect with missing dependency (stale closure risk)

## ✅ Clean — nothing to flag
No issues in this file. Mention what's done well if there's something
worth calling out (good defensive check, clean pattern, etc.)
```

---

#### Step 2 — Run it as a demo

After building a feature, type:

```
/code-review
```

Claude reads `SKILL.md`, loads `checklist.md` and `severity-guide.md`, gets the changed files from git, and applies all the rules. Students see output grouped by file with severity levels, exact rules cited, and a clean summary at the end.

---

#### Step 3 — Introduce `$ARGUMENTS`

Most of the time you want to review one file you just finished — not everything.

```
/code-review src/utils/balance.js
→ $ARGUMENTS = "src/utils/balance.js" → reviews that file only

/code-review
→ $ARGUMENTS = "" → reviews files from the last git commit
```

#### Step 4 — Demo both ways

```
/code-review src/utils/balance.js
```
Reviews just `balance.js`. Fast, focused. Great to run right after writing a function.

```
/code-review
```
Reviews whatever changed in the last commit. Run this before every push.

---

### Skill 2: `/accessibility-check`

**`.claude/skills/accessibility-check/SKILL.md`**

```markdown
---
name: accessibility-check
description: Audit Splitmate components for accessibility issues. Pass a file path to audit that file, or leave empty to audit files from the last git commit.
argument-hint: [file path]
---

# Accessibility Check

You are auditing Splitmate for accessibility issues. Every user should be able to
use this app with a keyboard only, with a screen reader, or with low vision.

## What to check

### Buttons and links
- Every <button> has either visible text or an aria-label attribute
- Icon-only buttons (trash, edit, close ×) must have aria-label="Delete expense" etc.
- No <div onClick> or <span onClick> — use <button> for actions, <a> for navigation
- Every <a> has descriptive link text — not "click here" or "read more"

### Forms
- Every <input>, <select>, and <textarea> is associated with a <label> using htmlFor + id
- Placeholder text is never the only label — it disappears when the user starts typing
- Required fields have the required attribute
- Field-level error messages use role="alert" so screen readers announce them
- Error messages are linked to their field with aria-describedby

### Images and icons
- Every <img> has an alt attribute (descriptive text for meaningful images, alt="" for decorative)
- Decorative icons that sit inside a labelled button have aria-hidden="true"

### Page structure
- Each page has exactly one <h1>
- Headings follow the correct order: h1 → h2 → h3. Never skip a level.
- Lists of items use <ul> or <ol> with <li>, not a chain of <div>s

### Color and meaning
- Balance amounts say both the amount AND who owes who in text
 ("You owe Shubham $800" — not just a red number)
- Pending member status uses text ("Pending") alongside the amber color
 — color alone is not enough for users who can't distinguish colors

### Keyboard navigation
- Tab order matches the visual reading order
- Focus ring is visible on all interactive elements (not hidden with outline: none)
- Modals (Add Expense) trap focus — Tab should cycle inside the modal, not escape it

## Output format
For each issue:
 File: [filename]
 Element: [the JSX element]
 Problem: [what's wrong and why it matters]
 Fix: [the corrected JSX]

At the end: a count of issues by severity (must fix / should fix / minor).

---

## Audit scope

Argument received: "$ARGUMENTS"

Look at the argument above:
- If it contains a file path — audit that file only.
- If it is empty — run `git diff --name-only HEAD~1` to get the list of files
 changed in the last commit, then audit each of those files.
```

**Usage:**

```
/accessibility-check src/pages/GroupDetailPage.jsx
/accessibility-check src/components/AddExpenseModal.jsx
/accessibility-check
```

The last form — no argument — audits whatever changed in the last commit. Useful to run before every PR.

---

### Skill 3: `/readme`

Smart README command — creates README.md if it doesn't exist, updates it if it does.

**`.claude/skills/readme/SKILL.md`**

```markdown
---
name: readme
description: Create README.md if it doesn't exist, or update it if it does. Reads CLAUDE.md and git log for context.
---

# README

First, check if CLAUDE.md exists. If it does, read it — it contains the project
conventions, data models, and current status. If it doesn't exist, infer what you
can from the src/ folder structure and package.json.

---

## If README.md does not exist

Create it from scratch. Explore the project and write a complete README
with the following sections in this order:

### Project title and description
One clear sentence explaining what Splitmate does and who it's for.

### Features
A bullet list of what the app actually does — from the user's perspective.
Not technical implementation. Things like:
- Create groups and add members by email (they can join before registering)
- Log shared expenses with equal or custom splits
- See who owes whom with auto-calculated settlements
- Soft-delete expenses without losing balance history
- Download monthly expense reports as CSV
- Group budget tracking with progress bar

### Tech stack
List the main technologies: React, Vite, Tailwind CSS, React Router, localStorage (Phase 1), Supabase (Phase 2).

### Getting started
Step-by-step from zero:
1. Clone the repo
2. Install dependencies (npm install)
3. Run the dev server (npm run dev)
4. Open http://localhost:5173

### Test accounts
List all pre-seeded accounts with credentials:
- shubham@test.com / password
- bob@test.com / password
- rahul@test.com / password
- eva@test.com / password

 these are created automatically on first load.

### Project structure
A brief map of the src/ folders:
- components/ — reusable UI pieces
- context/ — AuthContext (useAuth hook)
- pages/ — one file per route
- hooks/ — useGroups, useExpenses, useGroupBalances
- utils/ — balance.js, formatters.js, csvExport.js
- data/ — storage.js (all localStorage access)

### Available routes
A table with columns: Route | Page | Auth required
Include every route in the app.

### Data storage
Explain Phase 1 (localStorage, keys: sm_users, sm_groups, sm_expenses)
and mention Phase 2 (Supabase migration — see MCP section).

### Important notes
- Why expenses are soft-deleted (isDeleted: true) instead of removed
- Why paidBy uses member record IDs not userIds
- The pending member flow (add by email before they register)

Use plain markdown. No emoji. Write clearly for a developer picking up the
project for the first time.

---

## If README.md already exists

1. Read the current README carefully
2. If CLAUDE.md exists, read it for the latest project status
3. Run `git log --oneline -10` to see what changed recently
4. Compare what the README says against what's actually in the project
5. Update ONLY sections that are outdated or missing
6. Do not change the tone, formatting style, or section order
7. Do not remove sections — only update or add

After finishing, print exactly:
 Updated: [sections changed]
 Added: [new sections]
 Unchanged: [sections left as-is]
```

**Usage:**

```
/readme
```

---

### Skill 4: `/update-claude-md`

**`.claude/skills/update-claude-md/SKILL.md`**

```markdown
---
name: update-claude-md
description: Update CLAUDE.md with what was built or decided in this session. Patches only — does not rewrite the file.
---

# Update CLAUDE.md

Update CLAUDE.md with what changed in this session. Patch only — do not rewrite the whole file.

## Process

1. Read the current CLAUDE.md in full so you understand what's already there
2. Look through this conversation — what was built, changed, decided, or discovered?
3. For each piece of new information, find the right section in CLAUDE.md and update it in-place
4. If something new has no matching section, add it in the right place in the file
5. Never duplicate information already in the file
6. Never remove a section unless it's completely wrong or no longer relevant

## What to look for and where to put it

**Status checklist**
- Mark any features as done [x] that were completed this session
- Add any new planned items as [ ] if they came up in conversation
- Remove items that were planned but then cancelled or changed

**Data models**
- Update if any field was added, renamed, removed, or its type changed
- Update if a new data model was introduced (e.g. a new localStorage key)
- Fix any field descriptions that are now inaccurate

**Storage API / functions**
- Add any new functions that were created in storage.js
- Update signatures if a function's parameters or return value changed
- Remove functions that were deleted or renamed

**Routes**
- Add any new pages and their routes
- Update route descriptions if the page purpose changed
- Mark any routes that were removed

**Key rules and conventions**
- Add any new "always do X" or "never do Y" rules that were established
- Add project-specific patterns that Claude should follow when generating code
- Add warnings about gotchas discovered during this session

**Anti-patterns**
- Add anything that was tried and caused a bug or bad outcome
- Useful phrasing: "Never do X — it causes Y"

**Known issues**
- Add new bugs discovered this session with a brief description
- Remove issues that were fixed this session
- Add workarounds if a bug is known but not yet fixed

**Tech stack or dependencies**
- Update if a new package was installed (add it with its purpose)
- Update if a package was removed or replaced

**Environment and config**
- Add any new environment variables that were created
- Update if config files (vite.config.js, tailwind.config.js) were changed in a meaningful way

**Folder structure**
- Add any new folders or files that are now part of the project structure
- Update descriptions if existing folders changed purpose

## What NOT to do
- Don't rewrite sections that are still accurate
- Don't add vague summaries ("improved the codebase")
- Don't add things you're uncertain about
- Don't change the file's section order or heading names
- Don't add information that's already there in slightly different wording

## Output
After updating, print exactly:
 Added: [bullet list — what new information was added]
 Updated: [bullet list — what existing information was changed]
 Checked off: [bullet list — tasks marked as complete]
 Removed: [bullet list — anything removed or cleaned up]
```

**Usage:**

```
/update-claude-md
```

Run this at the end of every coding session to keep project memory current.

---

## 19. Plugins

📖 **Docs:** https://code.claude.com/docs/en/plugins | **Discover:** https://claude.com/plugins

> **What you'll learn:** Understand what plugins are, how they differ from skills and manual setup, and how to install one from the official marketplace.
>
> **What you'll build:** Install the official Playwright plugin and use it to test Splitmate's login and expense flow in a real browser.


### What Is a Plugin?

A plugin is a **packaged bundle** you install in one command that can contain any combination of:

- Skills (instruction sets for Claude)
- Slash commands
- Agents (specialized subagents)
- Hooks (automatic scripts)
- MCP servers (connections to external tools)

Instead of setting these up piece by piece yourself, someone else packages them together and you install the whole thing at once.

Think of it like installing an app on your phone. You don't wire up the notifications, storage, and UI separately — you just install the app and it all works.

---

### Standalone vs Plugin — What's the Difference?

You've been doing standalone configuration throughout this course — `.claude/commands/`, `.claude/skills/`, `.claude/settings.json`. Plugins bundle all of that into one installable unit.

Let's use a real example to see the difference.

---

#### Example: Code Review

**The standalone approach — what you built in §12:**

You created each piece separately:

**1. A slash command skill** (`.claude/skills/code-review/SKILL.md`) — the instruction Claude follows:

```markdown
# Code Review
Review the target for correctness, security, and code quality.
...
If $ARGUMENTS is empty, review the files changed in the last git commit.
```

**2. A hook** (`.claude/settings.json`) — runs automatically after every file write:

```json
{
 "hooks": {
 "PostToolUse": [{
 "matcher": "Write|Edit",
 "hooks": [{ "type": "command", "command": "npx eslint '$FILE_PATH' --format compact" }]
 }]
 }
}
```

**3. A skill** (`.claude/skills/code-review/SKILL.md`) — teaches Claude your project's specific rules:

```markdown
# Code Review Skill
Splitmate-specific checks:
- isDeleted filter must exist before any balance calculation
- paidBy must store member.id not userId
- No direct localStorage access outside storage.js
```

**4. MCP connection** — if you wanted Claude to post review comments directly to GitHub, you'd also wire up the GitHub MCP server manually.

Each piece works. But you set up four separate things, and if you move to a new project, you copy each file across and reconfigure from scratch.

---

**Now imagine a Code Review plugin:**

```
/plugin install code-review@claude-plugins-official
```

That one command installs:
- ✅ The `/code-review` slash command — pre-configured
- ✅ The PostToolUse hook — runs ESLint automatically after every write
- ✅ A skill teaching Claude best practices for code review
- ✅ GitHub integration — posts review comments directly to PRs

All four pieces. Wired together. Works on every project with one command.

You don't lose the standalone approach — it's still the right choice for Splitmate-specific conventions (your `react-component` skill, your CLAUDE.md rules). But for general tools that work anywhere, a plugin saves you the setup every time.

---

**The rule:**

| Use standalone when... | Use a plugin when... |
|---|---|
| It's specific to your project | It's useful across every project |
| You want full control over every detail | You want it working in 30 seconds |
| You're building your own conventions | Someone else already built it well |

---

#### Now: Playwright

The same comparison applies. You could set up Playwright standalone — or install the plugin.

**Standalone approach (what you'd have to do manually):**

```bash
# Install Playwright
npm install -D @playwright/test
npx playwright install chromium

# Add permissions
# Update settings.json to allow Bash(npx playwright *)

# Write test files yourself
# Run tests, paste raw output to Claude, debug manually
```

Claude reads raw terminal text. When a test fails, you copy the error and paste it back. You manage the test files yourself.

**Total setup: 15+ minutes. Claude sees text.**

**Plugin approach:**

```
/plugin install playwright@claude-plugins-official
```

Claude drives a real visible browser. Sees structured results. Knows the API. One command.

**Total setup: 30 seconds. Claude sees structured output.**

---

### The Official Marketplace

Anthropic maintains an official plugin marketplace built into Claude Code. Browse it anytime:

```
/plugin
```

This opens the plugin manager. Go to the **Discover** tab to see what's available. The official marketplace includes:

- **Code intelligence** — LSP plugins for TypeScript, Python, Go, Rust (jump to definition, type errors after every edit)
- **External integrations** — GitHub, Playwright, Supabase, Figma, Vercel, Sentry, Stripe, Firebase
- **Development workflows** — feature-dev, code-review, frontend-design, security-sweep

---

### Installing the Playwright Plugin on Splitmate

**Step 1 — Install from the official marketplace:**

```
/plugin install playwright@claude-plugins-official
```

No restart needed. Claude Code detects the plugin immediately.

**Step 2 — Confirm it's installed:**

```
/plugin
```

Go to the **Installed** tab — Playwright should be listed with its available commands.

**Step 3 — Start the Splitmate dev server in another terminal:**

```bash
npm run dev
```

Leave it running. Playwright needs the app at `localhost:5173`.

---

### Demo — Testing Splitmate in a Real Browser

**Test 1: Login flow**

```
Open a browser and go to localhost:5173.
Log in with shubham@test.com / password.
Confirm the dashboard loads and shows the "Your groups" section.
Take a screenshot when done.
```

A real Chrome window opens. Claude navigates to the app, fills in the login form, submits it, waits for the dashboard, and takes a screenshot. You watch it happen live.

**Test 2: Add an expense**

```
Continue from the dashboard (logged in as Shubham).
Open the "Goa Trip" group.
Click "Add expense".
Fill in: description "Team dinner", amount 2400, leave other fields as default.
Submit and confirm the expense appears in the list.
```

**Test 3: Test an error state**

```
Go back to the login page and try logging in with the wrong password
(shubham@test.com / wrongpassword).
Confirm the error message appears and we stay on the login page.
```

**Test 4: Mobile viewport**

```
Resize the browser to 375px wide (iPhone size).
Check the dashboard — does everything still fit and look correct?
Take a screenshot of any layout issues you find.
```

---

### From Manual Testing to Automated Tests

Once you've confirmed the flows work, ask Claude to write repeatable test files:

```
Write a Playwright test file for the login flow we just tested.
Save it to tests/login.spec.ts.
Cover: successful login, wrong password error, redirect to dashboard on success.
```

Then run the full suite:

```
Run all Playwright tests and show me the results.
```

---

### Manage Plugins

```
/plugin # Open plugin manager
/plugin install playwright@claude-plugins-official # Install from official marketplace
/plugin uninstall playwright # Remove a plugin
/reload-plugins # Reload without restarting
```

---


## 20. Understanding Agents

📖 **Docs:** https://code.claude.com/docs/en/sub-agents | https://code.claude.com/docs/en/agent-teams

> **What you'll learn:** Understand what agents, subagents, and agent teams are — and the key difference between them — before building with them in the next two lectures.
>
> **What you'll build:** Concept only — no new feature.


### The Problem They Solve

When you work with Claude normally, everything happens in one session. One context window. One thread of work. Claude reads files, thinks, writes code — all in the same place.

This works fine for most tasks. But sometimes you want to do multiple things at once, or you have a task so large it would overwhelm a single context window.

That's what agents solve.

---

### The Core Idea — A Session That Spawns Other Sessions

Think of your main Claude session as a manager. When the work gets big enough, the manager can hire workers to handle specific jobs. Each worker is a separate Claude instance with its own clean context, focused on one thing.

```
You
 └── Main Claude (the manager / orchestrator)
 ├── Worker A (separate Claude, own context)
 ├── Worker B (separate Claude, own context)
 └── Worker C (separate Claude, own context)
```

The workers are called **subagents**. The main Claude that spawns and coordinates them is called the **orchestrator**.

---

### Subagents — Workers That Report Back

A subagent is a separate Claude instance that:
- Gets one specific job
- Works in its own clean context (no noise from other work)
- Reports results back to the orchestrator when done

The orchestrator spawns it, waits for the result, and moves on.

**Real example:** You ask Claude to build two features at the same time.

```
Main Claude
 ├── Subagent A: "Build Group Settings page" → reports back when done
 └── Subagent B: "Build Settle Up button" → reports back when done
```

Both run simultaneously. Neither knows about the other. The orchestrator collects both results.

**Key rule:** Subagents can only talk back to the orchestrator. They cannot talk to each other.

---

### Agent Teams — Workers That Talk to Each Other

Agent Teams are different. Instead of workers that only report to the manager, teammates can communicate directly with each other — and they all share a task list.

```
Main Claude (Team Lead)
 │
 ▼
Shared Task List
 │
 ┌────┼────┐
 ▼ ▼ ▼
 A ←→ B ←→ C (teammates communicate directly)
```

Unlike subagents, which run within a single session and can only report back to the main agent, teammates work independently, each in its own context window, and can communicate directly with each other — not just through the lead.

**Real example:** A complex export feature where the data format affects how the UI and download both work.

```
Team Lead
 ├── Teammate A: "Design the CSV format" → tells B and C what format it decided
 ├── Teammate B: "Build the download logic" ← knows the format from A
 └── Teammate C: "Build the UI button" ← knows the format from A
```

B and C can ask A questions directly. They don't have to go through the Team Lead every time.

---

### The Key Difference

| | Subagents | Agent Teams |
|---|---|---|
| Communication | One-way → orchestrator only | Direct ↔ between teammates |
| Shared task list | ❌ No | ✅ Yes — teammates claim tasks |
| Setup needed | None | Enable in settings.json |
| Best for | Independent parallel tasks | Tasks where workers need to coordinate |
| Token cost | Lower | Higher (more communication) |

---

### When to Use Each

**Use subagents when:**
- Tasks are completely independent (no shared decisions)
- You want two features built simultaneously
- You want to audit code and then fix it

**Use agent teams when:**
- One teammate's decision affects another's work
- You need teammates to challenge each other's findings
- Tasks span frontend + backend + tests that need to coordinate

**Use neither when:**
- A simple prompt would do it
- The task is sequential with no parallelism
- You're editing the same file repeatedly

---

### Enabling Agent Teams

Agent teams are experimental and disabled by default. Enable them by adding this to `.claude/settings.json`:

```json
{
 "env": {
 "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1"
 }
}
```

Agent teams require Claude Code v2.1.32 or later. Check your version with `claude --version`.

Subagents need no setup — they work out of the box.

---

## 21. Subagents

📖 **Docs:** https://code.claude.com/docs/en/sub-agents

> **What you'll learn:** Use subagents to run parallel and sequential tasks — and see how isolated context makes each agent more focused and effective.
>
> **What you'll build:** Group Settings page and Settle Up button built in parallel. Codebase audited and fixed sequentially.

> **Note:** The examples below use **prompting** — you describe what each subagent should do directly in your prompt. This is the simplest approach. After the examples, there is a separate section on **agent files** (`.claude/agents/`) — the proper way to define named, reusable agents that Claude can invoke automatically.


### Built-in Subagents

Before you create custom subagents, Claude Code ships with three built-in ones it uses automatically:

| Agent | Model | What it does |
|---|---|---|
| **Explore** | Haiku | Read-only search — finds files and understands code without touching anything |
| **Plan** | Inherits | Research agent used in Plan Mode before presenting a plan |
| **General-purpose** | Inherits | Complex multi-step tasks needing both reading and writing |

You've been using these already without knowing — every time Claude says "let me explore the codebase first", it's delegating to the Explore subagent.

---

### Two Patterns

**Parallel — tasks that don't depend on each other**

```
Orchestrator
 ├── Subagent A: Build Group Settings page (runs now)
 └── Subagent B: Build Settle Up button (runs now)
```

Both finish faster than doing them one by one.

**Sequential — second task needs the first one's output**

```
Orchestrator
 ├── Subagent 1: Audit the codebase → produces a report
 └── Subagent 2: Read the report → apply the fixes
```

Subagent 2 waits for Subagent 1 to finish.

---

### Building Splitmate — Subagent Examples

**Example 1: Two features in parallel**

Group Settings and Settle Up are completely independent — safe to run simultaneously.

```
Build two features for Splitmate using parallel subagents.
All agents must read CLAUDE.md before starting.
The group creator is the admin — they have elevated permissions.

Subagent A — Group Settings page (/group/:id/settings):
 Only the group creator (admin) can access this page.
 Two actions only:
 1. Rename the group — save to Supabase
 2. Remove a member — only if they have no expenses in this group
 (not as payer and not in any expense_splits row).
 If they are involved, show: "Cannot remove — member has existing expenses."
 No delete group option. No other settings.

Subagent B — Settle Up button on Group Detail:
 Show a Settle Up button only on settlements involving the current user
 (they owe someone or someone owes them).
 Don't show it on settlements between two other people.
 When clicked: record the settlement in Supabase and remove it from the
 balance summary. The dashboard balance should update too.
```

**After both agents finish, verify in the browser:**

- Log in as Shubham (admin/creator) — confirm the Settings link appears
- Log in as Bob (not admin) — confirm the Settings link does NOT appear
- Try removing a member who has expenses — confirm the error message shows
- Try removing a member with no expenses — confirm it works and saves
- On the balance summary, confirm Settle Up only appears on rows involving the current user
- Click Settle Up — confirm the settlement disappears and the dashboard balance updates

---

**Example 2: Audit then fix — sequential**

Subagent 1 reads and reports. Subagent 2 reads that report and applies the fixes. Claude orchestrates both automatically.

```
Use two sequential subagents:

Subagent 1 — Read-only audit:
 Read all files in src/pages/, src/hooks/, and src/utils/.
 Find every place where expenses are accessed without filtering isDeleted.
 Find any place where one user's data could be visible to another.
 Do NOT change any files. Output a numbered list with file and line number.

Subagent 2 — Apply fixes (runs after Subagent 1 completes):
 Read the audit report from Subagent 1.
 Fix each issue. Run npm run build after all fixes to confirm no errors.
```

---

**Example 3: Seed data across groups — parallel**

Three subagents each build one group simultaneously.

```
Use parallel subagents to seed test data in Splitmate.

Subagent A — "Goa Trip" group:
 Members: shubham@test.com (creator), bob@test.com, rahul@test.com
 Add 5 expenses, mix of payers, some unequal splits.

Subagent B — "Bangalore Flat" group:
 Members: shubham@test.com (creator), eva@test.com
 Add 4 monthly recurring expenses, all split equally.

Subagent C — "Team Lunch" group:
 Members: bob@test.com (creator), rahul@test.com, eva@test.com
 Add 3 recent expenses. Bob paid all. Include one pending member.
```

**After all agents finish, verify in the browser:**

- Log in as shubham@test.com — dashboard should show 2 groups with balances
- Log in as bob@test.com — they should see 2 groups (Goa Trip + Team Lunch)

---

### Monitoring Running Subagents

While subagents are running, open Agent View in a **separate terminal** to see all sessions, their status, and which ones need your input:

```bash
claude agents
```

Agent View opens full-screen showing every active session. You can watch them work in parallel, check status, or step in if one needs you — while your main Claude session keeps running in the first terminal.

> **Note:** `claude agents` (terminal command) is different from `/agents` (slash command inside a session). `claude agents` opens Agent View. `/agents` just prints where your agent files are located.

---

### When Subagents Are Worth It

```
✅ Two or more independent features → parallel
✅ Audit before action → sequential
✅ Task too big for one context → split across subagents

❌ Simple 5-minute edits → overkill
❌ Tasks that share live state → keep in one session
❌ A slash command or skill would do it → use that instead
```

> **Note on agent files:** The prompting examples above are the starting point. For reusable, named agents with specific tool restrictions and models, see §23 — Custom Subagent Files — where we build a proper agent file for Splitmate.

---

## 22. Agent Teams

📖 **Docs:** https://code.claude.com/docs/en/agent-teams

> **What you'll learn:** Use agent teams for a complex task where teammates need to coordinate with each other — not just report back to the orchestrator.
>
> **What you'll build:** Export full group history — built by a 4-agent team where agents share a task list and the data agent's decisions directly affect the other agents.

> **Note:** The export history example below uses **prompting** — you describe roles in the prompt. After it, there is a section on the correct way to control and interact with agent teams using keyboard shortcuts, display modes, and subagent definitions.


### The Key Difference From Subagents

In the previous lecture, subagents only talked to the orchestrator. In an agent team, teammates can talk to each other directly and share a task list.

This matters when one agent's decision affects another's work. In the export feature:

- Agent 1 decides the CSV column format
- Agent 2 (download logic) and Agent 3 (UI button) both need to know that format
- Instead of both asking the orchestrator, they can ask Agent 1 directly

---

### Enable Agent Teams First

> **You do this** — add to `.claude/settings.json`:

```json
{
 "env": {
 "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1"
 }
}
```

Confirm it's enabled:

```
/team
```

---

### Building Splitmate — Export Full Group History

Every group has an expense history in Supabase. This feature lets members download it as a CSV. It's a good agent team task because the data formatting decision (column structure, member name lookup) must happen before the download logic and UI can be built.

```
Build "Export full group history" for Splitmate using an agent team.
Read CLAUDE.md before starting. Data comes from Supabase.

Agent 1 — Data formatting (runs first, others depend on its output):
 Query Supabase to get all expenses and members for a given group.
 Build src/utils/groupExport.js with an exportGroupHistory(groupId) function.
 Columns: Date | Description | Category | Amount | Paid By | Split Between | Notes
 Exclude deleted expenses (is_deleted = true). Use member names, not IDs.
 Sort by date ascending.
 Share the exact function signature and column format with the other agents when done.

Agent 2 — Download trigger (starts after Agent 1):
 Read the output from Agent 1 — understand the function signature.
 Add downloadGroupHistory(groupId, groupName) to groupExport.js.
 Calls exportGroupHistory, converts to CSV, triggers browser download.
 Filename: splitmate-[group-name]-history.csv

Agent 3 — UI (starts after Agent 1, runs parallel with Agent 2):
 Read the output from Agent 1 — understand what downloadGroupHistory expects.
 Add an "Export history" button to GroupDetailPage.jsx next to "+ Add expense".
 Disable with a tooltip if the group has no expenses.

Agent 4 — Build check (runs last):
 Run npm run build and confirm no errors. Report the result.
```

**After all agents finish, verify in the browser:**

- Open the Goa Trip group → click "Export history"
- Open the CSV and confirm: correct columns, member names (not IDs), no deleted expenses, sorted by date

---

### Why Agent 1 Must Go First

Agent 1 defines the data contract — the exact column structure and function name. Without it:
- Agent 2 doesn't know what `exportGroupHistory` returns
- Agent 3 doesn't know what `downloadGroupHistory` expects

This is the core coordination insight: **find the dependency, put that agent first, let everything else run in parallel after.**

---

### Talking to Teammates Directly

In **in-process mode** (the default):

```
↑ / ↓ arrows → select a teammate in the agent panel
Enter → open that teammate's session and message them
x → stop the selected teammate
Ctrl + T → toggle the shared task list
Escape → interrupt the selected teammate's current turn
```

In **split-pane mode** (requires tmux or iTerm2):
Click directly into a teammate's pane to interact with them.

---

### Controlling Agent Teams

**Keyboard shortcuts (in-process mode):**

```
↑ / ↓ arrows → select a teammate in the agent panel
Enter → open that teammate's session and message them
x → stop the selected teammate
Ctrl + T → toggle the shared task list
Escape → interrupt the selected teammate
```

**Display modes:**

```bash
{ "teammateMode": "auto" } # split panes when tmux/iTerm2 available
{ "teammateMode": "tmux" } # always use tmux split panes
```

Split panes require tmux or iTerm2. VS Code integrated terminal does not support it.

**Important limitations:**

- `/resume` and `/rewind` do not restore in-process teammates
- Teammates cannot spawn their own teammates
- Recommended team size: 3–5 teammates, 5–6 tasks per teammate

---

### When to Use Agent Teams

```
✅ One agent's decision affects another's work
✅ Parallel research where teammates challenge each other's findings
✅ Cross-layer features (frontend + backend + tests that need to coordinate)
✅ Complex features with 3+ genuinely interdependent parts

❌ Tasks where agents are truly independent → use subagents instead
❌ Sequential work with no parallelism → single session is fine
❌ Simple features → overkill
```

---


## 23. Custom Subagent Files

📖 **Docs:** https://code.claude.com/docs/en/sub-agents

> **What you'll learn:** Define reusable named agents as files — with fixed tool restrictions, a specific model, and a focused system prompt — so you can invoke the same specialist repeatedly without re-explaining its role each session.
>
> **What you'll build:** Four agents created in `.claude/agents/` and used together as a post-feature review workflow on the Settle Up feature.


### What Are Custom Subagent Files?

In §21 and §22 you described subagents in your prompt each time. That works, but every session you re-explain the same role, the same restrictions, the same context.

Agent files solve this. You define the agent once in `.claude/agents/<name>.md`. From that point on you just `@mention` it or Claude invokes it automatically based on its description.

---

### How They Differ From Skills and Prompts

| | One-time prompt | Skill | Agent file |
|---|---|---|---|
| Where instructions live | In your message | `.claude/skills/` | `.claude/agents/` |
| Reusable | ❌ | ✅ | ✅ |
| Tool restrictions enforced | ❌ | ❌ | ✅ |
| Fixed model | ❌ | ❌ | ✅ |
| Isolated context | ❌ | ❌ | ✅ |
| Auto-invoked | ❌ | ✅ | ✅ |

The critical difference from a skill: tool restrictions are **enforced**, not instructed. If you set `tools: Read, Grep, Glob` on an agent, it physically cannot write files regardless of what the prompt says.

---

### Folder Structure

Agent files live at `.claude/agents/<name>.md` — flat files, not subdirectories:

```
.claude/
├── agents/
│ ├── database-reviewer.md
│ ├── security-reviewer.md
│ ├── frontend-reviewer.md
│ └── fix-implementer.md
├── skills/
│ ├── explain-code/
│ └── react-component/
└── settings.json
```

---

### Agent File Format

```markdown
---
name: agent-name
description: When Claude should invoke this agent automatically. Write "Use proactively when..." for auto-delegation.
tools: Read, Grep, Glob
model: haiku
color: blue
---

Your system prompt here...
```

**Key fields:**

| Field | What it does |
|---|---|
| `name` | The @mention identifier |
| `description` | Tells Claude when to auto-invoke. Be specific. |
| `tools` | Allowlist — only these tools available |
| `disallowedTools` | Denylist — block specific tools, keep the rest |
| `model` | `haiku`, `sonnet`, `opus`, `fable`, or `inherit` |
| `color` | Visual identifier in the terminal |

---

### The Four Agents

---

**`.claude/agents/database-reviewer.md`**

#### Why Agent Files?

Before building the agent, it's worth understanding why a file is better than a prompt here.

Every time you finish a feature and want a database review, you'd have to re-explain: what the Splitmate schema looks like, what rules to check, what format to report in. With an agent file, you define it once. The rules, the model, the tool restrictions — all locked in. You just `@database-reviewer` and it already knows everything.

The other reason: **tool restrictions are enforced**. If you tell Claude in a prompt "only read files, don't write anything" — Claude tries to respect that but can make mistakes. With `tools: Read, Grep, Glob` in the frontmatter, the agent physically cannot write or run commands, regardless of what the prompt says.

---

#### Step 1 — Create the agent

Two ways to create an agent file:

**Option A — Ask Claude:**

```
Create a subagent file at .claude/agents/database-reviewer.md

It should be a read-only database reviewer for Splitmate.
- name: database-reviewer
- model: sonnet
- tools: Read, Grep, Glob only
- color: blue
- description: reviews Supabase queries, RLS policies, and data access patterns after any feature that touches the database

The system prompt should tell it to read CLAUDE.md for schema context before reviewing, check for isDeleted filters, correct member ID usage, RLS coverage, and N+1 patterns. Report issues with severity levels: Critical, High, Medium, Low.
```

Claude creates the file directly.

**Option B — Edit the file yourself:**

Create `.claude/agents/database-reviewer.md` manually and paste the content from Step 2 below.

---

#### Step 2 — Review and update the file

Open `.claude/agents/database-reviewer.md` and make sure it matches:

```markdown
---
name: database-reviewer
description: Reviews Supabase queries, RLS policies, and data access patterns for correctness and security issues. Use after any feature that touches the database layer.
tools: Read, Grep, Glob
model: sonnet
color: blue
---

You are a read-only database reviewer for Splitmate, a React + Supabase expense sharing app.

Read CLAUDE.md to understand the current schema before reviewing.

Review all database-related code for these issues:

## Data Integrity
- Expense queries missing is_deleted = false filter before calculations or display
- paidBy or splits[].memberId storing userId instead of member.id (group-scoped ID)
- balance.js reading expense.amount directly instead of splits[].amount

## RLS & Security
- Any component querying Supabase directly instead of going through storage.js
- Queries that could return data from groups the current user doesn't belong to
- Missing or incorrect RLS policy coverage for the changed tables

## Query Quality
- N+1 patterns — queries inside loops or .map() calls
- Fetching full rows when only a few columns are needed
- Missing indexes on frequently filtered columns

For each issue found, report:
 Severity: Critical / High / Medium / Low
 File and line number
 What's wrong
 What the fix should be

End with: "Database review complete. X issues found."
```

---

#### Step 3 — Invoke it

```
@database-reviewer review the Settle Up feature we just built
```

Claude delegates to `database-reviewer`. It reads the relevant files, applies the checklist, and returns a structured report. You get a clean summary — none of the raw file-reading output reaches your main context.

---

Now create the other three agents the same way — ask Claude to create each file, or edit them directly.

---

**`.claude/agents/security-reviewer.md`**

```markdown
---
name: security-reviewer
description: Reviews code for security vulnerabilities, exposed secrets, and auth issues. Use after any feature that touches authentication, user data, or environment variables.
tools: Read, Grep, Glob
model: sonnet
color: red
---

You are a read-only security reviewer for Splitmate.

Read CLAUDE.md before reviewing.

Review the changed code for these security issues:

## Secrets & Credentials
- Hardcoded API keys, tokens, or passwords in source files
- Environment variables accessed directly in components instead of through a config layer
- .env values that would be exposed in the client bundle

## Authentication & Authorization
- Routes accessible without authentication
- Actions that don't verify the current user has permission (e.g. non-admin editing group settings)
- Session data that includes the password field
- Any place userId is trusted from the client without server-side verification

## Data Exposure
- console.log statements that output user data, tokens, or sensitive fields
- Error messages that expose internal data structures
- API responses that include more fields than needed

## Input Handling
- User input rendered as raw HTML
- Form submissions without basic validation

For each issue found, report:
 Severity: Critical / High / Medium / Low
 File and line number
 What's wrong
 What the fix should be

End with: "Security review complete. X issues found."
```

---

**`.claude/agents/frontend-reviewer.md`**

```markdown
---
name: frontend-reviewer
description: Reviews React components for design consistency, accessibility, and code quality. Use after any feature that adds or modifies UI components.
tools: Read, Grep, Glob
model: haiku
color: green
---

You are a read-only frontend reviewer for Splitmate.

Read CLAUDE.md before reviewing — especially the design system section.

Review changed components for these issues:

## Design System
- Colours that don't match: primary #EA580C, background #F8F7F4, border #E8E4DE
- Inline style={{}} used instead of Tailwind classes
- Shadows or gradients (neither is part of the Splitmate design system)
- Inconsistent spacing or sizing compared to similar components

## Accessibility
- Buttons or interactive elements missing aria-label
- Form inputs missing associated <label> elements
- Icon-only buttons where screen readers would say just "button"
- Color used as the only way to convey information (e.g. balance status)

## React Quality
- Array index used as key instead of item.id
- useEffect with missing or incorrect dependency arrays
- State mutations (directly modifying objects instead of spreading)
- Functions recreated on every render that could use useCallback

## Code Consistency
- Component naming not matching the filename
- Props passed more than 2 levels deep without context
- Hardcoded test data or placeholder text left in

For each issue found, report:
 Severity: Critical / High / Medium / Low
 File and line number
 What's wrong
 What the fix should be

End with: "Frontend review complete. X issues found."
```

---

**`.claude/agents/fix-implementer.md`**

```markdown
---
name: fix-implementer
description: Applies approved fixes from review reports. Always receives a list of specific issues to fix. Never changes code not mentioned in the report.
tools: Read, Edit, Write, Bash, Grep, Glob
model: sonnet
color: orange
---

You are the fix implementer for Splitmate. You apply fixes based on a review report.

Rules:
- Fix only the issues explicitly listed in the report given to you
- Never refactor surrounding code
- Never change what isn't mentioned in the report
- Apply fixes one at a time, confirming each doesn't break the surrounding logic
- After all fixes, run: npm run build
- Report what you changed, which file, which line, and the build result

You know the Splitmate rules:
- All data access goes through storage.js — never direct Supabase calls from components
- isDeleted must be filtered before any balance calculation
- paidBy and splits[].memberId store member.id — never userId
- Tailwind only — no inline styles
```

---

### Invoke One Agent

After completing the Settle Up feature, test the database reviewer on its own:

```
@database-reviewer review the Settle Up feature we just built
```

Claude delegates to the `database-reviewer` agent. It reads the relevant files, checks against its rules, and returns a structured report. The raw file-reading output stays isolated — you see the summary.

**Monitor while it runs:**

Open a second terminal and run:

```bash
claude agents
```

Agent View opens full-screen and shows the `database-reviewer` session updating in real time — files being read, checklist applied, report being written. Your main Claude session continues in the first terminal.

```
Terminal 1 (main session) Terminal 2
────────────────────────── ──────────────────
@database-reviewer review... claude agents
 → database-reviewer: running...
```

---

### Sequential — One Agent Feeds the Next

**Example 1 — Fix flow:**

Run the database reviewer, then pass its report directly to the fix implementer:

```
Use database-reviewer to review the Settle Up feature.
Then pass its full report to fix-implementer and apply all Critical and High issues only.
```

The fix implementer reads the database reviewer's output and applies only the issues listed. Nothing else changes.

```
@database-reviewer reviews Settle Up
 │
 │ report passed directly
 ▼
@fix-implementer applies Critical + High fixes
 │
 ▼
npm run build → summary
```

---

**Example 2 — Consolidated review report:**

Instead of three separate reports, build one unified report where each reviewer adds its section without repeating what was already covered:

```
Build a consolidated review report for the Settle Up feature. Save it to reports/settle-up-review.md.

Step 1 — database-reviewer:
 Review the database layer and write its findings to reports/settle-up-review.md.
 Create the file with a header: "# Settle Up Feature Review"
 Add a "## Database" section with all issues found.

Step 2 — security-reviewer (runs after Step 1):
 Read the existing reports/settle-up-review.md.
 Add a "## Security" section below the Database section.
 Don't repeat issues already listed. Focus only on security concerns not covered.

Step 3 — frontend-reviewer (runs after Step 2):
 Read the existing reports/settle-up-review.md.
 Add a "## Frontend" section below the Security section.
 Don't repeat issues already listed. Focus only on UI and code quality concerns not covered.
```

The result: one clean file with three sections, no repeated findings. Run it before any PR.

```
@database-reviewer
 writes ## Database section
 │
 ▼
@security-reviewer
 reads existing file → adds ## Security section
 │
 ▼
@frontend-reviewer
 reads existing file → adds ## Frontend section
 │
 ▼
reports/settle-up-review.md
 (one file, three sections, no repetition)
```

---

### Parallel — Three Reviewers at Once, Reports Saved to Folder

All three reviewers run simultaneously in one prompt — each scans from a different angle and saves its own report file:

```
The Settle Up feature is complete. Create a reports/ folder if it doesn't exist.
Run three reviewers in parallel — each saves its report to the reports/ folder:

@database-reviewer — review files changed in the last git commit,
 save findings to reports/database-review.md

@security-reviewer — review files changed in the last git commit,
 save findings to reports/security-review.md

@frontend-reviewer — review files changed in the last git commit,
 save findings to reports/frontend-review.md
```

One prompt. Three agents start simultaneously. Each produces a detailed report in its own file. Your main context gets three clean summaries instead of three verbose scans.

---

### Fix Implementer After Parallel Reviews

After all three reviewers save their reports, review them yourself first — then run fix implementer:

```
All three review reports are saved in reports/.

Use fix-implementer to:
1. Read reports/database-review.md, reports/security-review.md, and reports/frontend-review.md
2. Apply all Critical and High severity issues across all three reports
3. Skip Low severity issues
4. Run npm run build after all fixes
5. Report what was changed, which file, and the build result
```

The fix implementer works through all three reports systematically, applies only the prioritised fixes, and confirms the build passes.

---

### Bonus — Everything in One Prompt (Discussion Only)

If you trust the agents and want to automate the full workflow without pausing to review, you can chain everything into a single prompt:

```
The Settle Up feature is complete. Create a reports/ folder if it doesn't exist.

Step 1 — Run three reviewers in parallel:

@database-reviewer — review files changed in the last git commit,
 save findings to reports/database-review.md

@security-reviewer — review files changed in the last git commit,
 save findings to reports/security-review.md

@frontend-reviewer — review files changed in the last git commit,
 save findings to reports/frontend-review.md

Step 2 — After all three reviewers finish:

Use @fix-implementer to read all three reports in reports/,
apply all Critical and High severity issues, run npm run build,
and report what was changed.
```

One prompt. Reviewers run in parallel. Fix implementer waits for all three to complete. Build runs at the end. The full review-and-fix cycle without any manual steps.

> **Why we don't implement this in this guide:** You want to read the reports before Claude starts changing files. Some "Critical" issues flagged by an automated reviewer may not actually be problems for your specific project. The two-step approach keeps you in control.

---

### The Complete Workflow

```
Feature completed (Settle Up)
 │
 ├── @database-reviewer ─┐
 ├── @security-reviewer ├── run in parallel
 └── @frontend-reviewer ─┘
 │
 Main Claude collects reports
 │
 @fix-implementer applies Critical + High fixes
 │
 npm run build
 │
 Final summary
```

> **This is not Agent Teams.** Main Claude is still the orchestrator — it spawns the reviewers, collects their reports, and passes them to fix-implementer. The agents don't talk to each other directly. Agent Teams (§22) is when agents need to communicate with each other to coordinate decisions. Here, each agent does its job independently and reports back.

---

### Scope — Project vs Personal

```
.claude/agents/database-reviewer.md ← committed, whole team gets it
~/.claude/agents/database-reviewer.md ← personal, all your projects
```

Commit project agents to the repo. Everyone on the team gets the same reviewers automatically.

---


## 24. Agent Teams in Practice

📖 **Docs:** https://code.claude.com/docs/en/agent-teams

> **What you'll learn:** Define agent team members as files — same as custom subagents in §23 — but now teammates coordinate directly with each other rather than just reporting back to the orchestrator.
>
> **What you'll build:** Build an email workflow for Splitmate's pending members — data analyst, content writer, brand reviewer, and email sender working as a real coordinated team.


### From Subagents to Teams — The Key Difference

In §23, the three reviewers reported back to main Claude. They didn't talk to each other.

In an Agent Team, teammates can **push back to each other directly** and **share a task list**. The Content Writer and Brand Reviewer in this example don't just report to the lead — they iterate together until they agree on the drafts.

```
Subagents (§23): Agent Teams (this section):

Main Claude Team Lead
 ├── Reviewer A → reports back ├── Agent A
 ├── Reviewer B → reports back ├── Agent B ←→ Agent C (direct communication)
 └── Reviewer C → reports back └── Agent D
```

---

### Enable Agent Teams First

Add to `.claude/settings.json`:

```json
{
 "env": {
 "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1"
 }
}
```

---

### The Workflow — Pending Member Onboarding Emails

Splitmate has pending members — people invited to a group who haven't registered yet. After 3 days, we want to send them a personalised nudge email.

This is a real Agent Team use case because two agents need to coordinate directly:
- **Content Writer** drafts the emails
- **Brand Reviewer** reviews and pushes back if the tone is wrong
- They iterate together until both agree — neither can unilaterally decide

---

### The Four Agent Files

**`.claude/agents/data-analyst.md`**

```markdown
---
name: data-analyst
description: Queries Supabase to gather lists of users for outreach campaigns. Use when you need a list of pending members, inactive users, or any user segment from the database.
tools: mcp__supabase
model: haiku
color: blue
---

You are a read-only data analyst for Splitmate.

Read CLAUDE.md for the current schema before querying.

When asked to find pending members:
- Query group_members where status = "pending"
- Filter for rows where joined_at was more than 3 days ago
- Join with groups to get the group name
- Return a structured list: { email, name, groupName, daysPending }

Return clean structured data only. No raw query output.
```

---

**`.claude/agents/content-writer.md`**

```markdown
---
name: content-writer
description: Drafts personalised emails for Splitmate users based on data provided. Works with brand-reviewer to iterate until emails are approved.
tools: Read, Write
model: sonnet
color: green
---

You are an email content writer for Splitmate.

When drafting pending member onboarding emails:
- Write a warm, friendly tone — not corporate
- Mention the specific group name the person was invited to
- Mention how many days they've been waiting
- Keep it short — 3 sentences max in the body
- Subject line: "Your friends are waiting for you on Splitmate"
- End with a clear CTA: "Join now and see what you owe"

Save each draft to drafts/<email-address>.md
Share drafts with brand-reviewer for approval before finalising.
```

---

**`.claude/agents/brand-reviewer.md`**

```markdown
---
name: brand-reviewer
description: Reviews email drafts for brand consistency and tone. Works directly with content-writer to iterate on drafts until approved. Use whenever emails are being drafted.
tools: Read, Write
model: sonnet
color: orange
---

You are the brand reviewer for Splitmate.

Splitmate's voice: friendly, direct, never pushy. Like a message from a friend, not a startup.

When reviewing email drafts from content-writer:
- Check tone — does it sound like a friend or a marketing email?
- Check length — should be under 80 words in the body
- Check the CTA — is it clear without being aggressive?
- Check personalisation — does it mention the group name and days pending?

If a draft needs changes, communicate directly with content-writer — tell them exactly what to fix.
If a draft is approved, mark it as "APPROVED" in the file and move on.
Do not send anything to email-sender until all drafts are approved.
```

---

**`.claude/agents/email-sender.md`**

```markdown
---
name: email-sender
description: Sends approved email drafts via the email MCP and logs results to Supabase. Only starts after brand-reviewer has approved all drafts.
tools: mcp__supabase, mcp__resend
model: haiku
color: red
---

You are the email sender for Splitmate.

Only process emails marked as "APPROVED" by brand-reviewer.

For each approved draft in drafts/:
1. Send the email via the Resend MCP
2. Log the result to Supabase — update the group_members row with sent_at timestamp
3. Report: email address, sent/failed, timestamp

If Resend MCP is not connected, simulate the send and log to Supabase only.
Report final counts: X sent, X failed.
```

---

### Layer 1 — Run as a Team (Prompt-based)

With the four agent files in place, the prompt to run the full workflow is short:

```
Run an agent team for pending member onboarding emails.

@data-analyst → find all pending members waiting 3+ days, share the list with the team

@content-writer → draft personalised emails for each person using data-analyst's list,
 share drafts with brand-reviewer

@brand-reviewer → review drafts as content-writer produces them, push back directly
 if anything needs changing, approve each draft when satisfied

@email-sender → send approved drafts via Resend MCP, log results to Supabase

Team Lead: data-analyst runs first. content-writer and brand-reviewer coordinate directly.
email-sender starts only after all drafts are approved.
```

---

### Layer 2 — The Coordination in Action

The moment that makes this a team and not just sequential subagents:

```
content-writer drafts email for bob@test.com
 │
 ▼
brand-reviewer reads draft
"Too corporate — rewrite the opening line, make it warmer"
 │
 ▼
content-writer revises
 │
 ▼
brand-reviewer reads revision
"Approved ✓"
 │
 ▼
email-sender sends
```

Content-writer and brand-reviewer are in direct conversation. Neither is going through the team lead. That back-and-forth is what Agent Teams enable that subagents cannot.

---

### Layer 3 — Adding the Email MCP (Production)

To actually send emails, connect Resend:

```bash
claude mcp add --transport http resend https://mcp.resend.com
```

Without it, `email-sender` simulates the send and logs to Supabase. The team workflow runs identically — just without real email delivery.

---

### The Complete Workflow

```
@data-analyst
 Queries Supabase for pending members 3+ days
 │
 ├─────────────────────────┐
 ▼ ▼
@content-writer @brand-reviewer
 Drafts emails ←→ Reviews + approves
 (they iterate together until agreement)
 │
 ▼
@email-sender
 Sends approved emails
 Logs to Supabase
 │
 ▼
 Final report: X sent, X failed
```

> **This is Agent Teams — not subagents.** Content-writer and brand-reviewer communicate directly and iterate together. They share a task list. The team lead coordinates the flow but doesn't mediate every message between teammates.

---



## 25. Spec-Driven Development

📖 **Docs:** https://code.claude.com/docs/en/advanced-usage

> **What you'll learn:** Write a complete feature specification before any code — letting Claude build against a clear source of truth instead of guessing intent.
>
> **What you'll build:** Monthly Report page built spec-first — Claude writes the spec, you review and approve it, then Claude implements and verifies against it.


### What Is Spec-Driven Development?
Spec-Driven Development means writing a **complete, structured specification** before any code is written. Claude reads the spec, understands exactly what's expected, implements it, and you verify it matches.

The spec is the single source of truth. No ambiguity, no back-and-forth.

### How SDD Differs from Normal Development

**Normal development:**
```
You: "Build a monthly report page"
Claude: Builds something
You: "The chart should show the last 6 months, not just this month"
Claude: Changes it
You: "The CSV filename should include the date"
Claude: Changes it
You: "Wait, the table columns are wrong"
→ 4+ rounds. Each correction costs time.
```

**Spec-Driven Development:**
```
You: "Write a spec for the monthly report page"
Claude: Writes a spec covering layout, data, edge cases, CSV format, empty states
You: Read it, add two things you noticed, approve it
You: "Now build it"
Claude: Reads spec → builds → done
→ 1 round. Everything was decided upfront.
```

### The SDD Workflow

```
Step 1: Ask Claude to write the spec
 ↓
Step 2: Review it — add missing cases, correct anything wrong
 ↓
Step 3: Approve it
 ↓
Step 4: Claude builds against the spec
 ↓
Step 5: Verify the result matches the spec
```

---

### Building Splitmate — Reports (Spec-Driven)

The Reports page is built here using Spec-Driven Development — spec first, then implementation. In the next lecture (GitHub CLI), this same feature gets taken through the full git workflow. We build it properly — spec first, then implementation.

**Step 1 — Ask Claude to write the spec**

```
Write a spec for the Reports feature in Splitmate.

The feature lets users see their expense history across all groups over time,
and download it as a CSV.

Write the spec as specs/reports.md. Cover:
- What the page looks like (layout, chart, table, controls)
- What data it shows and how it's calculated
- What the CSV contains
- Empty states and edge cases

Don't build anything yet — just write the spec.
```

Claude writes `specs/reports.md`. Read it. Add anything missing, correct anything wrong.

**Step 2 — Review and approve**

Read the spec carefully. For example, you might add:
- "The chart should show the last 6 months, not all time"
- "The CSV filename should be `splitmate-report-YYYY-MM.csv`"
- "If the user has no expenses, show an encouraging empty state — not just a blank page"

```
Update the spec with these changes:
[list your additions]

Now I approve it. Build the monthly report page exactly as the spec describes.
```

**Step 3 — Build against the spec**

```
Read @specs/reports.md carefully, then build:

1. The Reports page at /reports
2. The data aggregation logic in src/utils/monthlyReport.js
3. The CSV export utility in src/utils/csvExport.js

Build it to match the spec exactly. If anything in the spec is ambiguous,
ask before assuming.
```

**Step 4 — Verify**

```
Check the built Reports page against @specs/reports.md.

For each item in the spec, confirm it's implemented correctly.
List anything that doesn't match.
```

If there are gaps, fix them:
```
Fix items 2, 5, and 7 from the verification report.
```

---

### The Spec File

The spec Claude creates will look something like this — use it as a reference:

```
# Reports — Feature Spec
Last updated: April 2026

## What it does
A single page showing two things:
1. My Spending — a chart of how much the current user personally paid,
 broken down by group, over the last 6 months
2. Monthly Breakdown — a table of full group totals for a selected month,
 with a CSV download option

## Page layout (/reports)
- Page heading: "Reports"
- Month selector: dropdown, last 6 months, default = current month
 (both sections respond to this selector)

Section 1 — My Spending:
 Stacked bar chart (recharts)
 X-axis: last 6 month labels
 Y-axis: total amount the current user paid
 Each bar stacked by group — one muted color per group, legend below
 Tooltip on hover: group name + amount paid
 Only includes expenses where paidBy = current user's member ID

Section 2 — Monthly Breakdown:
 Table: Group | Total Spent | My Share | Owed To Me
 One row per group that has at least one expense in the selected month
 Sort: alphabetically by group name
 "Download CSV" button below the table

## CSV download
Filename: splitmate-report-YYYY-MM.csv (YYYY-MM = selected month)
Headers: Group,Total Spent,My Share,Owed To Me
Values: plain numbers (e.g. 1200.50, not $1,200.50)
Trigger: button click, no server needed
Disabled (greyed out) when there is no data for the selected month

## Data calculation

My Spending chart — per month, per group:
 amount I paid = sum of expense.amount where paidBy = current user's member ID

Monthly Breakdown table — per group, for selected month:
 totalSpent: sum of all expense.amount in that group for the month
 myShare: sum of splits[].amount where memberId = current user's member ID
 owedToMe: sum of expense.amount where paidBy = current user's member ID,
 minus their own share of those expenses

Both: round to 2 decimal places. Exclude isDeleted: true expenses.

## Edge cases
No expenses in selected month → hide table, show "No expenses this period", disable CSV
User paid nothing this month → chart shows no bars (or zero) for that month only
User in no groups → empty state for both sections
isDeleted expense → excluded from all calculations
```

---

### Spec Template

```markdown
# [Feature Name] — Spec

## What it does
One paragraph. The user's goal.

## Page layout
What sections, in what order.

## Data shown
What numbers/text appear and where they come from.

## Interactions
Buttons, forms, navigation — what happens when the user does each thing.

## Edge cases
Table: Scenario | Expected result

## Data calculation
Exact rules for any computed values.
```

### SDD Rules

```
The spec says WHAT — not HOW (no implementation details)
Edge cases are explicit — don't leave them as "obvious"
One concrete example with expected output where it helps

Bad: "Handle it gracefully" — too vague
Bad: "Use a HashMap" — that's implementation
Bad: "The UI should be nice" — not verifiable
Good: "Returns empty array when user has no expenses"
Good: "CSV filename format: splitmate-report-YYYY-MM.csv"
```

---

## 26. Claude Code: VS Code Extension

📖 **Docs:** https://code.claude.com/docs/en/vscode

> **What you'll learn:** Use Claude Code directly inside VS Code — same agent, same capabilities, with a diff view before every change is applied.
>
> **What you'll build:** Four polish prompts run on Splitmate — layout width, page titles, expense card redesign, and report chart improvement.


### What Is the VS Code Extension?

Claude Code runs in the terminal — but most developers live in VS Code. The extension brings Claude Code directly into your editor: same agent, same capabilities, but accessible from the sidebar without switching to the terminal.

```bash
# Install from VS Code marketplace:
ext install anthropic.claude-code

# Or from the terminal:
code --install-extension anthropic.claude-code
```

### What It Adds

| | Terminal | VS Code Extension |
|---|---|---|
| Chat interface | Terminal | Sidebar panel |
| File context | @file references | Click to add open files |
| Inline edits | Auto-applied | Diff view before accepting |
| See the change | After write | Before accepting |

The diff view is the biggest addition — before Claude applies a change, VS Code shows you exactly what will change. You can accept, reject, or ask Claude to revise before anything is written.

---

### What Works the Same

The `.claude` directory structure — including CLAUDE.md, skills, and configuration files — works with both approaches. These are workspace-level settings independent of the tool used to access them. Workflow patterns around slash commands transfer directly. The extension implements the same command set.

- **CLAUDE.md** — read automatically in both
- **Skills** — `/explain-code`, `/code-review`, `/impeccable` all work
- **Custom slash commands** — same `.claude/skills/` files, same invocation
- **MCP servers** — Supabase MCP configured in `.claude/settings.json` works
- **Plan Mode** — available in the extension

---

### Limitations Worth Mentioning

Custom scripts and hooks configured for CLI operation may need adaptation. The extension has different hook execution contexts.

| Feature | Terminal | VS Code Extension |
|---|---|---|
| **Hooks** | ✅ Full support | ⚠️ Partial — different execution context |
| **`/hooks` menu** | ✅ Yes | ❌ Not available |
| **Opus 4.6** | ✅ Available | ⚠️ Not always available |
| **`/effort` slider** | ✅ Yes | ⚠️ Inconsistent |
| **Agent Teams** | ✅ Yes | ⚠️ Experimental |
| **Checkpoint/rewind** | ✅ Yes | ⚠️ Limited |
| **Slash command list** | ✅ Complete | ⚠️ Incomplete — some CLI commands missing |

**The honest take for students:** The VS Code extension is great for reviewing diffs and working in the editor. For anything involving hooks, advanced models, or agent teams — use the terminal. Most you will use both depending on the task.

---

### Building Splitmate — Four Polish Prompts

These four prompts are run exactly as written. They demonstrate that well-written prompts work first time, and the VS Code diff view makes reviewing changes across multiple files quick and visual.

---

**Prompt 1 — Increase the app width**

```
The app feels too narrow after logging in — increase the max width for all
authenticated pages (Dashboard, Group Detail, Create Group, Profile, Reports).
Also increase the navbar width to match.

The landing page width is fine — don't change it.

Use a wider max-width like max-w-5xl or max-w-6xl for the main content area
and navbar on protected pages. Make sure it stays centered and has consistent
horizontal padding on smaller screens.
```

---

**Prompt 2 — Update page titles**

```
Update the page title (browser tab title) for each route in Splitmate.

Currently every page shows "Splitmate". Update them to:
- / → Splitmate — Split expenses, not friendships
- /login → Sign In | Splitmate
- /register → Create Account | Splitmate
- /dashboard → Dashboard | Splitmate
- /group/new → New Group | Splitmate
- /group/:id → [Group Name] | Splitmate
- /group/:id/settings → Group Settings | Splitmate
- /profile → Profile | Splitmate
- /reports → Reports | Splitmate

Use the document.title API or React Helmet — whichever is already in the project.
```

---

**Prompt 3 — Redesign Group Detail page (Impeccable)**

```
/impeccable redesign the entire Group Detail page at src/pages/GroupDetailPage.jsx

It currently works but looks generic. Give it a proper two-column layout:
- Left column: expense list (wider, main focus)
- Right column: balance summary and member list (narrower, supporting info)

On mobile, stack them — expenses first, then balance and members below.

Also redesign each section individually:
- Expense list: clean cards with clear hierarchy — description large, amount prominent,
 paid by and split info smaller below
- Balance summary: visually distinct settlements, color-coded (red = owes, green = is owed),
 amount stands out
- Member list: compact, shows active vs pending status clearly

Keep all existing functionality. Match the design system: orange #EA580C,
off-white #F8F7F4 background, no shadows, no gradients.
```

---

**Prompt 4 — Improve the report chart (Impeccable)**

```
/impeccable improve the spending chart on the Reports page:

1. Make the chart horizontally scrollable if all months don't fit in the visible area.
2. Show the total amount above each bar.
3. Show the amount on hover as a tooltip.
4. If there is no data (no expenses for the selected period or group/category),
 still show an empty 6-month chart so the page doesn't look broken.
```

---

### Why the VS Code Extension for These Prompts?

Prompts 3 and 4 use `/impeccable` — a design skill that touches multiple components. The VS Code diff view lets you see every file change before accepting, which is especially useful for design prompts where Claude might touch more files than expected.

Prompts 1 and 2 are layout and metadata changes — the diff view makes reviewing changes across multiple page files quick and clear.

---


## 27. GitHub CLI

📖 **Docs:** https://code.claude.com/docs/en/github-actions

> **What you'll learn:** Understand what `gh` CLI adds over plain `git` — and why it's specifically needed before Claude Code Web.
>
> **What you'll build:** Group Budget feature built on a feature branch, taken through the full git workflow: branch → build → verify → commit → push → PR → merge.


### Plain git vs GitHub CLI — What's the Difference?

Throughout this course, Claude Code has been running git commands directly through the Bash tool. No `gh` needed:

```bash
git checkout -b feature/profile-page # Claude creates branches
git add . # Claude stages changes
git commit -m "feat: add profile" # Claude commits
git push origin feature/profile-page # Claude pushes
```

This all works with plain `git` — Claude runs these commands the same way you would.

**PRs are different.** PRs are a GitHub platform feature, not a git concept. Plain `git` has no idea what a PR is.

`git` handles the repository — commits, branches, merging locally. PRs live on GitHub's platform — they're a web interface feature for reviewing and merging branches.

To open a PR from the terminal, you need `gh`:

```bash
gh pr create # opens a PR on GitHub — not possible with plain git
gh pr merge # merges via GitHub — not possible with plain git
gh run list # checks CI status — not possible with plain git
```

---

### Why gh Is Essential for Claude Code Web

Claude Code Web runs in Anthropic's cloud and needs to clone your GitHub repo. The `/web-setup` command (run in the next lecture) links your `gh` token to your Claude account — that's how Claude Code Web gets access to your repositories.

Without `gh` CLI set up and authenticated, Claude Code Web can't clone your project.

---

### Prerequisites

```bash
# macOS
brew install gh

# Windows
winget install GitHub.cli

# Authenticate:
gh auth login
```

---### Building Splitmate — Group Budget (via Git Workflow)

Group Budget lets the creator of a group set an optional total budget (e.g. $20,000 for a trip). The group detail page shows a progress bar — how much has been spent vs the budget. A warning appears when adding an expense that would push the group over.

We build it on a feature branch, take it through the full git workflow, and merge it into main.

---

**Step 1 — Create a feature branch**

```
Create a new git branch called feature/group-budget
```

Claude runs:
```bash
git checkout -b feature/group-budget
```

---

**Step 2 — Plan first (Shift+Tab)**

```
Plan — don't code yet.

We want to add an optional budget field to Splitmate groups.
The group creator can set a total trip budget (e.g. $20,000).

Think through:
1. Where the budget is stored (on the group object)
2. Where it's displayed — Group Detail header and/or Dashboard card
3. What counts toward the budget — total spent by the group, not per person
4. What happens when expenses exceed the budget
5. Whether adding a budget is part of Create Group or Group Settings
6. Edge cases — no budget set, budget is zero, all expenses deleted

Do NOT write code yet.
```

Review the plan, approve it, then proceed.

---

**Step 3 — Build the feature**

```
Implement the group budget feature as planned.

Summary:
- Add an optional budget field to the group object in storage.js (null if not set)
- In Group Settings: let the creator set or update the budget (number input, optional)
- In Group Detail: if a budget is set, show a progress bar below the group name
 "$8,200 spent of $20,000 budget" with a visual bar
 Bar turns red when over 90% spent, orange between 70–90%, green below 70%
- In Add Expense modal: if adding this expense would exceed the budget, show a warning
 "This will put the group $1,800 over budget" — still allow saving, just warn
- On Dashboard group cards: if a budget is set, show a small "$X of $Y" line

Do not change the balance calculation — budget is display only.
```

---

**Step 4 — Verify in the browser:**

- Open Group Settings for "Trip to Goa" — set budget to $20,000
- Go to Group Detail — confirm the progress bar shows correct amounts
- Add an expense that would exceed the budget — confirm the warning appears
- Go to Dashboard — confirm the budget indicator shows on the group card
- Remove the budget in Group Settings — confirm the progress bar disappears

---

**Step 5 — Commit**

```
Stage all changes and write a conventional commit message for the group budget feature.
```

Claude runs:
```bash
git add .
git commit -m "feat: add optional group budget with progress tracking

- Optional budget field on group object (set via Group Settings)
- Progress bar in Group Detail: spent vs budget, color-coded by %
- Warning on Add Expense when new expense would exceed budget
- Budget indicator on Dashboard group cards
- No effect on balance calculations — display only"
```

---

**Step 6 — Push and open a PR**

```
Push this branch and open a pull request to main.
Title it based on what we built and include how to test it manually.
```

Claude runs:
```bash
git push origin feature/group-budget
gh pr create \
 --title "feat: optional group budget with progress tracking" \
 --body "## What this adds
Groups can now have an optional total budget set by the creator.

## How to test
1. Open Group Settings for any group
2. Set a budget (e.g. $20,000)
3. Go to Group Detail — progress bar should show
4. Add an expense that exceeds the budget — warning should appear
5. Check Dashboard — budget indicator visible on group card
6. Remove the budget in Settings — progress bar should disappear"
```

---

**Step 7 — Apply review feedback (optional)**

> This step only applies if a teammate reviews the PR and leaves comments.
> In a solo learning session, skip this and go straight to Step 8.

If someone reviews the PR and leaves comments:

```
Apply all the feedback from the PR.
```

---

**Step 8 — Merge**

```
All checks are passing. Merge the PR into main.
```

Claude runs:
```bash
gh pr merge --squash --delete-branch
git checkout main
git pull origin main
```

---

### Useful GitHub CLI Commands

```bash
gh pr list # See all open PRs
gh pr create # Open a new PR
gh pr view 12 # See PR details
gh issue create --label "bug" # Create a bug report
gh run list # Check CI status
gh run view --log # See CI logs
```

---

## 28. Claude Code Desktop

📖 **Docs:** https://code.claude.com/docs/en/desktop-quickstart

> **What you'll learn:** Use the native desktop app — drag and drop files, global hotkeys, multi-window sessions — and apply the Spec-Driven Development workflow. Then use GitHub CLI (§25) to push the work and open a PR.
>
> **What you'll build:** Profile page built using SDD — spec written first, built, verified, then pushed to GitHub and a PR opened.


### Installation

```bash
# macOS: native installer
curl -fsSL https://claude.ai/install.sh | bash
# Or download .dmg from claude.ai/download

# Windows: PowerShell
irm https://claude.ai/install.ps1 | iex
# Or download .exe from claude.ai/download
```

### Desktop-Specific Features

**Global Hotkey:** `Cmd+Shift+C` (macOS) / `Ctrl+Shift+C` (Windows)
→ Opens Claude from any app — your browser, Figma, wherever you are

**File Drag & Drop:**
```
Drag a profile photo → Claude adds it as the default avatar
Drag a screenshot → Claude sees it and fixes the layout
Drag a Figma export → Claude matches it in Tailwind
```

**Multi-Window:**
```
Window 1: Claude working on the profile form
Window 2: Claude running validation in parallel
Both read CLAUDE.md — consistent decisions across windows
```

| | Terminal | Desktop |
|---|---|---|
| Speed | Fastest | Fast |
| Drag & drop | ❌ | ✅ |
| Global hotkey | ❌ | ✅ |
| Multi-session | Manual | ✅ Built-in |
| Screenshot paste | Manual | ✅ Native |

---

### Building Splitmate — Profile Page (Spec-Driven)

The Profile page doesn't exist yet. We build it the same way we built the Reports page in §23 — spec first, then build, then verify against the spec. This time in the Desktop app.

---

**Step 1 — Write the spec**

```
Write a detailed spec for the Splitmate Profile page and save it to specs/profile.md.

The Profile page is at /profile (protected route).

Cover all of these in the spec:
- What information is shown: name, email (read-only), join date (read-only), profile photo
- Editing name — how the user updates it and saves
- Changing password — current password + new password, validation rules
- Profile photo upload — accepted file types, where it's stored, fallback if none uploaded
- How the navbar updates when the name changes
- Error states for each field
- Mobile vs desktop layout

The user cannot edit their email address — it is always display-only.
Write the spec clearly enough that another developer could build it without asking questions.
```

Claude writes the spec and saves it to `specs/profile.md`. Read through it — this is what we're building to.

---

**Step 2 — Drag in the default avatar**

Download or create a simple circular SVG avatar. Drag it into the Claude Desktop window:

```
I've dragged in a default-avatar.svg file.
Save it to public/default-avatar.svg — this is the fallback when no photo is uploaded.
```

---

**Step 3 — Build against the spec**

```
Build the Profile page by implementing everything in @specs/profile.md exactly.

Use Supabase Auth for password changes.
Store profile photos in Supabase Storage.
Email is display-only — never editable.
Don't add anything not in the spec.
```

---

**Step 4 — Verify against the spec**

```
Read @specs/profile.md and check the built Profile page against it.

For each item in the spec, confirm whether it is implemented correctly.
List anything that is missing or doesn't match.
```

If there are gaps:

```
Fix items 2 and 5 from the verification — the password validation
and the navbar update on name change.
```

---

**Step 5 — Drag in a screenshot**

Take a screenshot of the Profile page in the browser. Drag it into Claude Desktop:

```
Here's what the Profile page looks like: [drag screenshot]

The Change Password section is hard to find — it's below the fold on mobile.
Move it above the profile photo section. Also the Save button needs to be
more prominent.
```

Claude sees the screenshot and makes targeted fixes without you describing every detail.

---

**Step 6 — Two windows for parallel polish**

```
Window 1: Add a loading spinner while the profile photo is uploading to Supabase
Window 2: Add validation — name must be at least 2 characters, new password 8+ characters
```

**After Claude finishes, verify in the browser:**

- Edit name → saves to Supabase, navbar updates immediately
- Change password → works with Supabase Auth, rejects wrong current password
- Upload photo → shows preview, persists after page reload
- No photo uploaded → default-avatar.svg shows as fallback
- Open `specs/profile.md` and confirm every item is implemented

---

**Step 7 — Push and open a PR**

Now that the feature is built and verified, use GitHub CLI (set up in §25) to push and open a PR:

```
Create a feature branch for the profile page, stage all changes,
write a conventional commit message, push the branch, and open a PR to main.
Include a brief description of what was built and how to test it.
```

Claude runs `git checkout -b`, `git add .`, `git commit`, `git push`, and `gh pr create` — the full workflow without leaving the Desktop app.

---



## 29. Claude Code Web

📖 **Docs:** https://code.claude.com/docs/en/claude-code-on-the-web

> **What you'll learn:** Run Claude Code sessions in Anthropic cloud — your GitHub repo is cloned automatically, sessions persist when you close the browser, and you can monitor them from the mobile app.
>
> **What you'll build:** Two new features — Active/Settled group tabs on Dashboard, and Add Member to existing group.


### What Is Claude Code Web?

Claude Code Web runs in Anthropic-managed cloud VMs at claude.ai/code. It is the full Claude Code agent with your repository cloned — not just a chat interface.

Sessions persist even if you close your browser, and you can monitor them from the Claude mobile app.

| Feature | Local Terminal | Claude Code Web |
|---|---|---|
| Installation | Required | None |
| Your files | Local filesystem | Cloud VM — repo cloned from GitHub |
| Run dev server | Yes | No |
| Session persists if browser closes | No | Yes |
| Monitor from mobile | No | Yes |
| From any device | Your machine only | Any browser |

---

### Why GitHub CLI Comes First

Claude Code Web needs access to your GitHub repo to clone it. After setting up GitHub CLI in the previous lecture, run one command in your terminal to link it:

```
/web-setup
```

This syncs your local gh CLI token to your Claude account. Once done, Claude Code Web can clone Splitmate and push branches automatically.

---

### What Carries Over From Your Repo

Anything committed to your repo is available in cloud sessions:

| | Available |
|---|---|
| CLAUDE.md | Yes |
| .claude/settings.json | Yes |
| .claude/skills/ | Yes — all your custom commands work |
| .mcp.json MCP servers | Yes |
| Personal ~/.claude/CLAUDE.md | No — not in repo |
| Locally installed packages | No — fresh VM |

---

### Building Splitmate — Two New Features

**Feature 1 — Active / Settled tabs on the Dashboard**

```
Add two tabs to the Dashboard: "Active" and "Settled".

Active tab (default): groups where at least one member has a non-zero balance.
Settled tab: groups where all balances are zero — everyone is even.

A group with no expenses at all should appear in Active (nothing to settle yet).
Show a count on each tab: "Active (3)" and "Settled (1)".
Use orange #EA580C for the selected tab.
```

**Verify in the browser:**

- Group with unsettled balances → Active tab
- Settle all balances → moves to Settled tab
- Group with no expenses → stays in Active

---

**Feature 2 — Add a new member to an existing group**

```
Add an "Add member" option to Group Settings (/group/:id/settings).
Admin only (group creator).

Input: email address.
- Registered user → add as active immediately
- Not registered → add as pending

The new member is NOT added to any existing expenses or splits.
They start fresh for future expenses only. Save to Supabase.
```

**Verify in the browser:**

- Add registered email → appears as active
- Add unregistered email → appears as pending
- Confirm new member not in existing expense splits
- Only group creator sees Add member

---


## Quick Reference Card

### Installation (April 2026)

```bash
# macOS / Linux / WSL:
curl -fsSL https://claude.ai/install.sh | bash

# Windows PowerShell:
irm https://claude.ai/install.ps1 | iex

# macOS Homebrew (no auto-update):
brew install --cask claude-code

# Windows WinGet (no auto-update):
winget install Anthropic.ClaudeCode

# Verify:
claude --version && claude doctor
```

### Most Used Commands

```bash
claude # Start interactive session
claude -p "prompt" # Non-interactive output
claude --model claude-opus-4-7 # Start with Opus

/model sonnet # Switch to Sonnet 4.6
/model haiku # Switch to Haiku 4.5
/model opusplan # Opus planning + Sonnet execution
/effort xhigh # Maximum reasoning depth
/compact # Compress context
/clear # Fresh start
/cost # Token usage this session
/review # Review recent changes
/init # Generate CLAUDE.md for this project
```

### Model Guide (July 2026)

```
Haiku 4.5 → Small edits, cosmetic fixes, quick tasks
Sonnet 5 → Default for 90% of coding work
Opus 4.8 → Architecture, hard bugs, full codebase review
Fable 5 → Days-long tasks, large migrations, maximum capability
opusplan → Opus planning + Sonnet implementation
```

### Context Management

```
Starting a new feature → /clear → CLAUDE.md auto-loaded → @mention relevant files
Mid-session, getting slow → /compact
Switching to simple task → /model haiku
Parallel independent work → Subagents
Full complex feature → Agent Team
Read-only audit → claude --defaultMode plan
After every session → /update-claude-md
```

### Splitmate Feature Map

| § | Lecture | What Gets Built |
|---|---|---|
| §02 | Setup & Installation | Project scaffolded and running |
| §03 | Start Project | Landing page + complete working app — auth, groups, add expense, balance |
| §03 | (design prompt) | Visual design — orange theme, navbar, cards, consistent styling |
| §04 | Plan Mode | Unequal expense split · Balance accumulation bug fix · Pending member split bug fix |
| §05 | Slash Commands | Concept only |
| §06 | Session & Conversation Management | Concept only — workflow skill used throughout |
| §07 | Models & Usage | Expense split breakdown per card (Haiku) · Pending member balance bug (Sonnet) · Expense edit flow (Sonnet) · Full balance audit with fixes (Opus xhigh) |
| §08 | When Claude Gets It Wrong | Concept only — Escape, /rewind, git, scope narrowing |
| §09 | CLAUDE.md | CLAUDE.md written for the project |
| §10 | Tools | Concept only — no new feature |
| §11 | Bash Mode | No new feature — practical bash prompts on Splitmate |
| §12 | Permissions | No new feature — configures settings.json |
| §13 | Context | Expense notes field · Debug by pasting real errors |
| §14 | Context Window | Expense categories built as example — token cost measured with /status and /cost |
| §15 | Hooks | Dev tooling — auto-Prettier, bash logger, destructive command blocker |
| §16 | MCP | Supabase migration — schema, RLS, dummy data, app updated |
| §17 | Skills | Four skills: explain-code (demo) · react-component · accessibility · impeccable |
| §18 | Custom Slash Commands | Four workflow skills — /code-review, /accessibility-check, /readme, /update-claude-md |
| §19 | Plugins | Playwright plugin installed — tests login, expense flow, error states in real browser |
| §20 | Understanding Agents | Concept only — orchestrator, subagents, agent teams explained |
| §21 | Subagents | Group Settings page · Settle Up button (prompting approach) |
| §22 | Agent Teams | Export full group history — CSV download from Group Detail (prompting approach) |
| §23 | Custom Subagent Files | Four reusable agents: database-reviewer, security-reviewer, frontend-reviewer, fix-implementer |
| §24 | Agent Teams in Practice | Pending member email workflow — data analyst, content writer, brand reviewer, sender |
| §25 | Spec-Driven Development | Reports page — spec written first, then built against it |
| §26 | Claude Code: VS Code Extension | Width fix · Page titles · Group Detail redesign · Report chart improvement |
| §27 | GitHub CLI | Group Budget — progress bar, warning when over budget, full git workflow |
| §28 | Claude Code Desktop | Profile page — spec written first, built and verified using SDD workflow |
| §29 | Claude Code Web | Active/Settled group tabs · Add member to existing group |

---

*This guide is current as of July 2026. Claude Code updates frequently — run `claude doctor` and check code.claude.com/docs for the latest.*


---

# Additional Resources

## Project Workflow (Git / GitHub)

> **When to use:** Cover this if students are new to Git and GitHub, or want to see how Claude Code fits into a real version control workflow. Skip if you already knows Git.
>
> **What you'll build:** Splitmate repo initialised, pushed to GitHub, and a day-to-day feature branch workflow established.


### Why Git Matters in Claude Code

Claude Code runs git commands directly through the Bash tool — `git checkout`, `git add`, `git commit`, `git push`. You don't need to type them yourself. But you need to understand the workflow so you can tell Claude what to do and verify it's doing the right thing.

---

### Step 1 — Initialise the Repo

If you haven't already set up a git repo for Splitmate:

```
Initialise a git repo for this project, create a .gitignore for a Vite + React project,
stage everything, and make the first commit with the message "initial: Splitmate scaffold"
```

Claude runs:
```bash
git init
# creates .gitignore
git add .
git commit -m "initial: Splitmate scaffold"
```

---

### Step 2 — Create the GitHub Repo and Push

```
Create a new GitHub repository called splitmate and push this project to it
```

Claude runs:
```bash
gh repo create splitmate --public --source=. --remote=origin --push
```

Your project is now on GitHub. Every commit from here on can be pushed with `git push origin main`.

---

### Day-to-Day Workflow

**Start of every session:**

```bash
git checkout main
git pull origin main # get latest before branching
```

---

### Scene 1 — Solo Developer

You're the only one working on the project. No PRs needed — merge directly into main.

**Step 1 — Create feature branch:**

```
Create a feature branch called feature/temp
```
`git checkout -b feature/temp`

**Step 2 — Build and test:**

```
Create a file called temp.txt with the text "This is a temp file"
```

Test it — confirm the file exists.

**Step 3 — Commit:**

```
Stage all changes and commit with a conventional commit message
```
```bash
git add .
git commit -m "feat(groups): add group budget with progress bar"
```

**Step 4 — Merge into main locally:**

```
Switch to main and merge feature/temp, then delete the feature branch
```
```bash
git checkout main
git merge feature/temp
git branch -d feature/temp
```

**Step 5 — Push to GitHub:**

```
Push main to GitHub
```
`git push origin main`

---

### Scene 2 — Team / Collaborative

Others need to review your work before it goes into main. Push the branch, open a PR, get feedback, then merge.

**Step 1 — Create feature branch:**

```
Create a feature branch called feature/temp
```
`git checkout -b feature/temp`

**Step 2 — Build and test:**

```
Create a file called temp.txt with the text "This is a temp file"
```

Test it — confirm the file exists.

**Step 3 — Commit and push the branch:**

```
Stage all changes, commit with a conventional message, and push the branch to GitHub
```
```bash
git add .
git commit -m "feat(groups): add group budget with progress bar"
git push origin feature/temp
```

**Step 4 — Open a PR:**

```
Open a PR for this branch with a short description of what was built
```
`gh pr create --title "feat: group budget" --body "Adds budget field to groups with progress bar and warning when over budget"`

→ Team reviews on GitHub, leaves comments, requests changes if needed.

**Step 5 — Merge after review:**

```
Merge the PR, switch to main, pull latest, and delete the feature branch
```
```bash
gh pr merge --squash
git checkout main
git pull origin main
git branch -d feature/temp
```

---

### The Difference

| | Solo | Team |
|---|---|---|
| Feature branch pushed? | ❌ Local only | ✅ Pushed to GitHub |
| PR opened? | ❌ | ✅ For review |
| Merge method | `git merge` locally | `gh pr merge` via GitHub |
| Others can see work before main? | ❌ | ✅ |

---

### Prompt to Do the Full Workflow in One Go

**Solo — at the end of a feature:**

```
The feature is done and tested.
Stage all changes, commit with a conventional message, switch to main,
merge the feature branch, delete it locally, and push main to GitHub.
Tell me each command you run.
```

**Team — at the end of a feature:**

```
The feature is done and tested.
Stage all changes, commit with a conventional message,
push the branch to GitHub, and open a PR with a short description.
Tell me each command you run.
```

---

## Fable 5 — Dark Mode for Splitmate

> **When to use this:** At the end of this guide, after all features are built. Dark mode touches every page, every component, and every colour in the design system simultaneously. It's the kind of task where Fable 5's sustained reasoning over a large codebase genuinely outperforms Opus.

Dark mode isn't just toggling colours. Done properly it requires:
- A consistent token system for every colour in the design system
- Updating every component simultaneously without inconsistencies
- Persisting the user's preference across sessions
- Smooth transitions between modes
- Ensuring sufficient contrast in both modes

This is exactly the kind of sustained, multi-file, detail-intensive task where Fable 5 earns its cost.

---

### Step 1 — Switch to Fable 5

```bash
/model fable
```

---

### Step 2 — Run the dark mode prompt

```
Add dark mode to Splitmate. This should be production-quality — not just inverting colours.

Design system:
Light mode (current):
 Primary: #EA580C (orange)
 Background: #F8F7F4 (warm off-white)
 Surface: #FFFFFF (cards, modals, inputs)
 Border: #E8E4DE
 Text: #1C1917 (primary), #57534E (muted)

Dark mode:
 Primary: #EA580C (keep orange — it works on dark)
 Background: #1C1917 (warm near-black — matches the light mode text)
 Surface: #292524 (cards, modals, inputs)
 Border: #44403C
 Text: #F5F5F4 (primary), #A8A29E (muted)

Requirements:
1. Add a toggle button in the Navbar — sun/moon icon, no label
2. Persist the preference in localStorage under "splitmate_theme"
3. Apply the theme class to the <html> element so Tailwind dark: classes work
4. On first load, respect the system preference (prefers-color-scheme)
5. Add smooth 200ms transition on background and text colour changes
6. Update every page and component to use dark: variants

Update the design system colours in CLAUDE.md after completing.
Run npm run build to confirm no errors.
```

---

### Step 3 — Verify in the browser

**After Claude finishes, verify in the browser:**

- Toggle appears in Navbar — sun/moon icon, no label
- Toggle switches between light and dark correctly
- Refresh the page — preference is remembered
- Open in a new incognito window — system preference is respected
- Check every page: Dashboard, Group Detail, Profile, Reports, Group Settings
- Check modals: Add Expense, Group creation
- Confirm orange primary colour works in both modes
- Confirm text is readable in both modes (no grey-on-grey)

---

### Why Fable 5 for This

Dark mode on a project this size means updating 20+ components consistently. A single missed `bg-white` or `text-gray-900` creates an inconsistency that's annoying to hunt down.

Fable 5 holds the entire design token system in context while updating every component — it tracks what it's changed, notices inconsistencies as it goes, and finishes with a coherent result.

Opus 4.8 would handle most of this. But Fable 5 tends to be more thorough on the final pass — catching the edge cases (modals on mobile, loading states, empty state illustrations) that Opus sometimes misses on large multi-component changes.

---

