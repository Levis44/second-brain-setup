---
name: vault-setup
description: Interactive Obsidian vault configurator. Two rounds of 4 questions, shows a vault preview for confirmation, then builds the vault structure directly using the Obsidian CLI — no manual folder selection needed.
---

# Vault Setup — Interactive Obsidian Configurator

Run from INSIDE the Obsidian vault folder (or any folder you want to become the vault).

**TWO AskUserQuestion calls (4 tabs each), then a confirmation, then build.**

---

## ROUND 1 — Who you are (4 questions, one call)

```
AskUserQuestion([
  {
    question: "What best describes how you primarily use a computer for work?",
    header: "Your role",
    options: [
      { label: "Business Owner", description: "Running a team or company — decisions, operations, growth" },
      { label: "Developer / Builder", description: "Writing code, shipping products, building tools" },
      { label: "Consultant / Freelancer", description: "Client work, project delivery, multiple relationships" },
      { label: "Creator / Podcaster", description: "Making content — videos, newsletters, podcasts" }
    ]
  },
  {
    question: "What consumes most of your mental bandwidth right now?",
    header: "Bandwidth",
    options: [
      { label: "Team & people", description: "Hiring, managing, keeping everyone aligned" },
      { label: "Clients & revenue", description: "Pipeline, relationships, deals, delivery" },
      { label: "Projects & execution", description: "Shipping things, hitting deadlines, tracking progress" },
      { label: "Ideas & strategy", description: "Figuring out what to do next, research, planning" }
    ]
  },
  {
    question: "How do you prefer to capture notes and ideas?",
    header: "Capture style",
    options: [
      { label: "Daily brain dumps", description: "Write everything to a daily note and sort later" },
      { label: "Straight into folders", description: "Each note goes directly where it belongs" },
      { label: "Voice or transcripts", description: "I dictate or paste transcripts, want Claude to clean and file them" },
      { label: "No system yet", description: "That's exactly why I'm here" }
    ]
  },
  {
    question: "Work only, or personal life too?",
    header: "Scope",
    options: [
      { label: "Work only", description: "Business, clients, projects — keep it professional" },
      { label: "Work + personal goals", description: "Include priorities and things outside work too" },
      { label: "Full life OS", description: "One place for everything — work, personal, health, finance, ideas" },
      { label: "Start work, add later", description: "Work first, expand when ready" }
    ]
  }
])
```

---

## ROUND 2 — What you need (4 questions, one call)

```
AskUserQuestion([
  {
    question: "What's fallen through the cracks most in your work?",
    header: "Biggest pain",
    options: [
      { label: "People & follow-ups", description: "Things I said I'd do with team or clients that got forgotten" },
      { label: "Decisions & reasoning", description: "I make decisions but lose the context and why behind them" },
      { label: "Ideas & opportunities", description: "Good ideas I had but never acted on" },
      { label: "Projects & deadlines", description: "Things in progress that stall because I lost track" }
    ]
  },
  {
    question: "Do you have existing files to import?",
    header: "Existing files",
    options: [
      { label: "Yes — a lot", description: "PDFs, Word docs, slides, old notes — years of stuff" },
      { label: "Yes — a handful", description: "A few key files to start with" },
      { label: "Starting fresh", description: "No existing files — building from zero" },
      { label: "Not sure yet", description: "I'll figure this out later" }
    ]
  },
  {
    question: "How often do you make decisions you'd want to reference later?",
    header: "Decisions",
    options: [
      { label: "Daily — constantly", description: "I'm deciding things all day and need to track the reasoning" },
      { label: "Weekly — meaningful ones", description: "A handful of significant decisions per week" },
      { label: "Rarely — only big ones", description: "Only major or irreversible decisions need capturing" },
      { label: "Never tracked before", description: "Open to starting — just haven't done it" }
    ]
  },
  {
    question: "What do you most want Claude Code to do in this vault?",
    header: "Daily goal",
    options: [
      { label: "Faster decisions", description: "Surface the right context so I can decide quickly" },
      { label: "Track everything", description: "Nothing falls through — people, projects, commitments" },
      { label: "Write in my voice", description: "Use my past notes to match my tone when drafting" },
      { label: "All of the above", description: "I want the full compounding system" }
    ]
  }
])
```

---

## PREVIEW — Show the proposed vault before building

After both rounds, show the proposed structure as a formatted preview. Do NOT create anything yet.

Output exactly this format:

```
Here's your vault — ready to build when you are.

📁 [vault name based on current directory]
├── inbox/          Drop zone — everything new lands here first
├── daily/          Daily brain dumps and quick captures  
├── [role folder 1]/  [one line purpose]
├── [role folder 2]/  [one line purpose]
├── [role folder 3]/  [one line purpose]
├── projects/       Active work with status and next actions
├── archive/        Completed work — never deleted, just moved
└── scripts/        File processing tools

Slash commands that will be installed:
  /daily    — start your day with vault context
  /tldr     — save any session to the right folder
  /[role]   — [role-specific command description]

vault-context.md will be written here — Claude reads it every session
to know who you are. No re-explaining yourself ever again.

Type "build it" to create this now, or tell me what to change.
```

Wait for the user to confirm ("build it", "yes", "go", "looks good", etc.) or request changes.
If they request changes, adjust the structure and show the preview again.

---

## BUILD — Only after confirmation

### Step 1: Create folder structure via bash

```bash
mkdir -p inbox daily [role-specific folders] projects archive scripts \
  .claude/skills/daily .claude/skills/tldr .claude/skills/[role-command]
```

Role-specific folders:
- Business Owner → `people/ operations/ decisions/`
- Developer → `research/ clients/`
- Consultant → `clients/ research/`
- Creator → `content/ research/ clients/`

If scope includes personal → also `personal/` and `goals/`

### Step 2: Open the vault in Obsidian via CLI

```bash
open -a Obsidian "$(pwd)"
```

This opens the current folder directly as an Obsidian vault. No manual folder selection.

### Step 3: Write vault-context.md

```markdown
# About Me

[2 sentences — role, what matters most, what they're optimizing for]

# My Vault Structure

[Actual folder tree, one-line purpose per folder]

# How I Work

- Capture style: [from answer]
- Biggest pain point: [from answer]  
- Decision frequency: [from answer]
- Scope: [work only / work + personal / full life OS]

# Context Rules

When I mention a decision → check decisions/ for related past decisions first
When I mention a person, client, or project → look in [relevant folder] first
When I ask you to write something → read recent daily/ notes to match my voice
When I drop a file in inbox/ → ask if I want it sorted now or later
```

### Step 4: Write skill files

**`.claude/skills/daily/SKILL.md`** — read today's note or create one, check inbox/, surface top 3 priorities, ask "What are we working on?"

**`.claude/skills/tldr/SKILL.md`** — summarize conversation, save to right folder, update memory.md

Role-specific:
- Business Owner → `.claude/skills/standup/SKILL.md`
- Developer → `.claude/skills/project/SKILL.md`
- Consultant → `.claude/skills/client/SKILL.md`
- Creator → `.claude/skills/content/SKILL.md`

### Step 5: Write memory.md

```markdown
# Memory

## Session Log
[Updated by Claude Code after each session]

## My Preferences
[Added as Claude learns them]
```

### Step 6: Final output — clean and short

```
Done. Your vault is live in Obsidian.

One thing left: Settings → General → Enable Command Line Interface
(This lets other tools and scripts talk to Obsidian directly.)

Add this to any project's CLAUDE.md to pull in your vault context:

  At the start of every session, read [absolute path]/vault-context.md

Your slash commands work now:
  /daily    — run this first thing tomorrow morning
  /tldr     — run this at the end of any session
  /[role]   — [one liner]

Have files to import?
  python scripts/process_docs_to_obsidian.py ~/your-files inbox/
  Then: "Sort everything in inbox/ into the right folders"
```
