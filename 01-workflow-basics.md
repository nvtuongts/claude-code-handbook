# Professional Claude Code Workflow Guide
### Apply a Structured Workflow to Every Project

> Stop winging it. This guide gives you a structured, repeatable workflow — Requirements → Clarification → Planning → Implementation → Verification — that **automatically applies to every project** via Claude Code's global configuration layer.

---

## How Claude Code Configuration Works (The Hierarchy)

Claude Code loads configuration from multiple locations, from broadest to narrowest scope:

```
~/.claude/CLAUDE.md          ← Global: loaded in EVERY project, EVERY session
~/.claude/commands/          ← Global commands: available in ALL projects
~/.claude/settings.json      ← Global hooks & settings: apply everywhere
~/.claude/skills/            ← Global skills: available in all projects
        │
        ▼  (project layer OVERRIDES or EXTENDS global)
        │
./CLAUDE.md                  ← Project memory: this project only
./.claude/commands/          ← Project commands: this project only
./.claude/settings.json      ← Project hooks: this project only
```

**The key insight:** Everything placed in `~/.claude/` is your personal global setup — it follows you into every project automatically. You set it up once, and it just works everywhere.

---

## The Professional Workflow: 5 Phases

```
REQUIREMENTS → CLARIFY → PLAN → IMPLEMENT → VERIFY
     ↑                                           |
     └───────── track progress, loop back ───────┘
```

---

## The Feature Directory: One Folder Per Feature

Every feature gets a **single self-contained directory** under `docs/features/`. All artifacts for that feature — requirements, plan, progress, verification report — live together in one place.

```
docs/features/
└── user-authentication/             ← one directory per feature
    ├── README.md                    ← master doc: links all artifacts, shows current status
    ├── requirements.md              ← what we agreed to build
    ├── plan.md                      ← implementation plan + live task checklist
    └── verification.md              ← verification report (filled in at verify phase)
```

This means to understand a feature end-to-end — what was required, how it was planned, what was built, whether it passed — you open one folder. No hunting across directories.

`docs/features/` is committed to git alongside your code, giving you a permanent record of every feature's lifecycle.

---

## Part 1: Global Setup (Do This Once, Ever)

This is a 45-minute investment that pays off on every project forever.

### Step 1: Create Your Global `~/.claude/CLAUDE.md`

This file is loaded at the start of **every** Claude Code session, in every project. Use it for personal working preferences, universal conventions, and your workflow rules.

```bash
mkdir -p ~/.claude
touch ~/.claude/CLAUDE.md
```

```markdown
# My Global Claude Code Configuration

## Who I Am
I am a senior software engineer. I build production-grade software.
Always treat me as an experienced developer — skip basic explanations
unless I ask for them.

## My Universal Workflow Rules
1. NEVER write code before showing me a written plan and getting my approval
2. ALWAYS use Plan Mode (Shift+Tab) for tasks touching more than 2 files
3. ALWAYS read existing code before suggesting changes — never assume structure
4. When uncertain about requirements, ask — don't guess and implement
5. After completing any task, run the project's test/lint commands from CLAUDE.md

## Feature Tracking Convention
All feature work is tracked under `docs/features/<feature-name>/`:
- requirements.md  — what was agreed to build
- plan.md          — implementation plan + task checklist (live progress)
- verification.md  — verification report
- README.md        — master status doc linking all artifacts

Always read `docs/features/<feature-name>/plan.md` at the start of
an implementation session to find the current state and next task.

## My Coding Preferences (All Projects)
- TypeScript strict mode — always explicit types, no `any` unless absolutely justified
- Descriptive variable names — no abbreviations except well-known ones (e.g., `i`, `ctx`, `req`, `res`)
- Explicit error handling — no silent failures, no empty `catch` blocks
- Small, focused functions — if a function does more than one thing, split it
- No magic numbers or strings — use named constants or enums
- Environment variables for all config — never hardcode secrets or environment-specific values

## Session Startup Ritual
At the start of every session, before doing anything else:
1. Read this global CLAUDE.md
2. If a project CLAUDE.md exists, read it
3. If a ROADMAP.md exists, read it
4. Check docs/features/ for any feature with status "In Progress"
5. Tell me: what project we're in, what's in progress, and what to focus on

## Progress Tracking Rules
- Task progress lives in `docs/features/<feature-name>/plan.md` as checkboxes
- Update checkboxes [ ] → [x] as each task completes — do not wait until the end
- If the context window fills up mid-task, update the plan file checkboxes before stopping
- ROADMAP.md is the high-level backlog; feature folders are the detailed tracking

## Communication Style
- Be concise. I don't need explanations of what you just did — I can read the diff.
- When something is ambiguous, state the ambiguity and ask. Don't pick one and proceed.
- Flag risks and tradeoffs proactively, especially for architectural decisions.
- If you're about to do something irreversible (delete, overwrite, deploy), confirm first.
```

---

### Step 2: Create Global Slash Commands (`~/.claude/commands/`)

These commands work in **every project**. They encode the full professional workflow.

```bash
mkdir -p ~/.claude/commands
```

Create the following 6 files:

---

**`~/.claude/commands/start.md`** — Orient to any project at session start
```markdown
Start a new work session. Do the following in order:

1. Read ~/.claude/CLAUDE.md (global preferences)
2. If ./CLAUDE.md exists, read it (project context)
3. If ./ROADMAP.md exists, read it (project backlog)
4. Run `git log --oneline -5` to see recent commits
5. Check docs/features/ for any README.md with status "🚧 In Progress"

Then give me a 3-5 line summary:
- What project this is and what it does
- What was recently completed (from git log)
- What feature is currently in progress (from docs/features/)
- What I should work on next (from ROADMAP.md)

Do NOT start any work. Just orient me.
```

---

**`~/.claude/commands/clarify.md`** — Forces clarification before any work
```markdown
A new requirement needs clarification before any work begins.

The feature name is: $ARGUMENTS

Do the following:
1. If docs/features/$ARGUMENTS/requirements.md exists, read it. Otherwise ask me to describe the requirement.
2. Read relevant existing code to understand the current system
3. Identify what is ambiguous, missing, or could cause problems
4. Ask me 3-5 focused, specific questions (not generic ones)
5. Wait for my answers
6. Write a clear "Agreement Summary" of what we've agreed to build
7. Save the agreed requirements to docs/features/$ARGUMENTS/requirements.md
8. Update docs/features/$ARGUMENTS/README.md status to "📋 Requirements Agreed"

Do NOT start planning or writing code yet.
```

---

**`~/.claude/commands/plan.md`** — Generates a written plan inside the feature folder
```markdown
Create a detailed implementation plan for feature: $ARGUMENTS

Follow this process exactly:

**Phase 1 — Research (read only, no changes)**
- Read docs/features/$ARGUMENTS/requirements.md
- Read all relevant existing source files related to this feature
- Understand current architecture, patterns, and conventions
- Note any potential conflicts or dependencies

**Phase 2 — Write the plan**
Save to docs/features/$ARGUMENTS/plan.md:

```
# Plan: [Feature Name]
**Status:** Not Started
**Created:** [date]

## Goal
What we're building and why (summarized from requirements).

## Research Summary
What exists today that's relevant. What patterns to follow.

## Approach
The strategy. Key decisions and why. Tradeoffs considered.

## Tasks
- [ ] 1. [First task — small, concrete, testable]
- [ ] 2. [Second task]
- [ ] 3. ...

## Files to Create / Modify
- `path/to/file.ts` — what changes and why

## Acceptance Criteria
- [ ] Criterion 1
- [ ] Criterion 2

## Risks
- Risk 1 and mitigation
```

**Phase 3 — Update README and present**
Update docs/features/$ARGUMENTS/README.md:
- Set Status to "📐 Plan Ready — Awaiting Approval"
- Link to plan.md

Then show me the plan and say:
"Please review docs/features/$ARGUMENTS/plan.md. Edit it directly if needed. Reply 'approved' to proceed."

Do NOT write any code until I approve.
```

---

**`~/.claude/commands/implement.md`** — Executes an approved plan task by task
```markdown
Implement the feature: $ARGUMENTS

Before writing a single line of code:
1. Read docs/features/$ARGUMENTS/plan.md fully
2. Confirm status is not already "✅ Complete"
3. Find the first unchecked [ ] task
4. Update plan.md Status to "🚧 In Progress"
5. Update docs/features/$ARGUMENTS/README.md status to "🚧 In Progress"

Then for each task:
- Announce: "Starting task N: [task name]"
- Implement it
- Run relevant tests if available
- Update plan.md: [ ] → [x] immediately after completing the task
- Announce: "✅ Task N done. Starting task N+1..."

Rules:
- If you encounter something not in the plan, STOP and ask me before proceeding
- If tests fail, fix them before moving to the next task
- If the context window is getting full, save all checkbox state to plan.md first
- After all tasks: update plan.md Status to "Implementation Complete — Awaiting Verification"

Do not mark the feature complete — that's /verify's job.
```

---

**`~/.claude/commands/verify.md`** — Runs full verification and saves report
```markdown
Verify the implementation for feature: $ARGUMENTS

Steps:
1. Read docs/features/$ARGUMENTS/plan.md — get acceptance criteria
2. Read CLAUDE.md for project-specific test/lint/build commands
3. Run all verification commands (tests, lint, type check, build)
4. Check each acceptance criterion — mark [x] if met, leave [ ] if not
5. Do a smoke test of the main use case

Save the full report to docs/features/$ARGUMENTS/verification.md:

```
# Verification Report: [Feature Name]
**Date:** [date]

## Automated Checks
- [✅/❌] Tests:   `npm run test` — X passed, Y failed
- [✅/❌] Build:   `npm run build` — success / failed
- [✅/❌] Lint:    `npm run lint` — clean / N errors
- [✅/❌] Types:   `npx tsc --noEmit` — clean / N errors

## Acceptance Criteria
- [✅/❌] Criterion 1 — met/not met because...
- [✅/❌] Criterion 2 — met/not met because...

## Smoke Test
What was manually tested and result.

## Overall: ✅ PASSED / ❌ FAILED
```

If anything fails: fix it, re-run, update the report.

If everything passes:
- Update docs/features/$ARGUMENTS/plan.md Status to "✅ Complete — [date]"
- Update docs/features/$ARGUMENTS/README.md status to "✅ Complete — [date]"
- Tell me: "Ready to commit. Suggested message: feat($ARGUMENTS): [one line summary]"
```

---

**`~/.claude/commands/new-project.md`** — Bootstrap a brand new project with the full workflow structure
```markdown
Bootstrap the professional workflow for this new project.

Create the following structure:

1. **`CLAUDE.md`** in project root — analyze the codebase (or ask me about the project
   if it's empty) and populate:
   - What this project does
   - Tech stack
   - Project structure (key directories and what they contain)
   - Key commands: build, test, lint, run
   - Code conventions specific to this project
   - Architecture rules
   - Definition of Done

2. **`ROADMAP.md`** in project root:
   ```
   # Roadmap

   ## ✅ Completed
   (none yet)

   ## 🚧 In Progress
   (none yet)

   ## 📋 Backlog
   - [ ] [First feature or task — add real items here]
   ```

3. **Directory structure:**
   ```
   docs/
   └── features/        ← all feature work tracked here
   .claude/
   ├── commands/        ← project-specific commands (optional)
   └── settings.json    ← language-specific hooks
   ```

4. **`.claude/settings.json`** — starter hooks placeholder:
   ```json
   {
     "hooks": {
       "PostToolUse": []
     }
   }
   ```

After creating everything, show me the CLAUDE.md and ask:
"Does this look right? What should I add or change before we start?"
```

---

### Step 3: Configure Global Hooks (`~/.claude/settings.json`)

```bash
touch ~/.claude/settings.json
```

```json
{
  "hooks": {
    "SessionStart": [
      {
        "matcher": "",
        "hooks": [
          {
            "type": "command",
            "command": "echo '📂 Project: '$(basename $(pwd))"
          }
        ]
      }
    ]
  }
}
```

> Keep global hooks minimal. Put language-specific hooks (eslint, prettier, etc.) in the **project-level** `.claude/settings.json`.

---

### Verify Your Global Setup

```bash
ls ~/.claude/
# Should show: CLAUDE.md  commands/  settings.json

ls ~/.claude/commands/
# Should show: start.md  clarify.md  plan.md  implement.md  verify.md  new-project.md
```

Launch Claude Code in any directory and type `/help` — you should see all 6 commands listed under "Personal commands."

---

## Part 2: Per-Project Setup (Do This Once Per Project, ~15 Minutes)

### For a New Project

```bash
cd my-new-project && claude
/new-project
```

Claude creates `CLAUDE.md`, `ROADMAP.md`, `docs/features/`, and `.claude/`. Review and edit `CLAUDE.md`.

### For an Existing Project

```bash
cd my-existing-project && claude
/init
```

Then manually add to the generated `CLAUDE.md`:

```markdown
## Key Commands
- Dev:     `npm run start:dev`       ← watch mode with hot reload
- Build:   `npm run build`           ← compile TypeScript
- Start:   `npm run start:prod`      ← run compiled output
- Test:    `npm run test`            ← unit tests (Jest)
- Test E2E:`npm run test:e2e`        ← end-to-end tests
- Coverage:`npm run test:cov`        ← test coverage report
- Lint:    `npm run lint`            ← ESLint check
- Fix:     `npm run lint -- --fix`   ← ESLint auto-fix
- Types:   `npx tsc --noEmit`        ← type check without emitting files

## Architecture Rules
- One module per domain (tasks, users, auth) — never mix domain logic across modules
- Business logic lives in services only — controllers handle HTTP, nothing else
- Use DTOs with class-validator for all request bodies — never trust raw request data
- Repository pattern for all database access — no raw queries in services
- Config via ConfigModule + .env only — never hardcode secrets or environment values

## Definition of Done
A task is complete ONLY when:
1. Feature works as specified
2. `npm run build` succeeds — no TypeScript compilation errors
3. `npm run test` passes — no failing unit tests
4. `npm run lint` is clean — no ESLint errors
5. `npx tsc --noEmit` is clean — no type errors
6. All acceptance criteria in docs/features/<n>/plan.md are checked
```

Add language-specific hooks to `.claude/settings.json`:

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write(*.ts)",
        "hooks": [{ "type": "command", "command": "npx eslint $file --fix --quiet 2>/dev/null" }]
      }
    ]
  }
}
```
---

## Part 3: The Daily Workflow (Repeat For Every Feature)

### Starting Any Session

```
/start
```

Claude reads all context, checks `docs/features/` for in-progress work, and orients you in seconds.

### Working on a Feature: Step by Step

**Step 1 — Create the feature folder and write your requirement**

```bash
mkdir -p docs/features/user-authentication
```

Create `docs/features/user-authentication/README.md`:
```markdown
# Feature: User Authentication
**Status:** 📝 Draft

## Overview
JWT-based authentication with login, registration, and role-based access control.

## Artifacts
- [Requirements](requirements.md)
- [Plan](plan.md)
- [Verification](verification.md)
```

Create `docs/features/user-authentication/requirements.md` (you write this):
```markdown
# Requirements: User Authentication

## Background
The API currently has no authentication. All endpoints are publicly accessible,
which is a security risk before going to production.

## Goal
Secure all API endpoints with JWT authentication and add role-based access control (RBAC).

## Constraints
- Must not break existing endpoints — add auth as a layer, not a rewrite
- Passwords must be hashed (bcrypt) — never stored in plain text
- Access tokens expire in 1h, refresh tokens in 7d

## Out of Scope
- OAuth / social login (Google, GitHub)
- Two-factor authentication (2FA)
```

**Step 2 — Clarify** (new session)
```
/clear
/clarify user-authentication
```
Claude asks focused questions, you answer, it saves the agreed requirements back to `requirements.md`.

**Step 3 — Plan** (new session)
```
/clear
/plan user-authentication
```
Review `docs/features/user-authentication/plan.md` in your editor. Edit it. When happy:
```
Approved. Proceed.
```

**Step 4 — Implement** (new session)
```
/clear
/implement user-authentication
```
Claude works task by task, updating checkboxes in `plan.md` as it goes.

**Step 5 — Verify**
```
/verify user-authentication
```
Verification report is saved to `docs/features/user-authentication/verification.md`.

**Step 6 — Commit**
```bash
git add -A
git commit -m "feat(user-authentication): add JWT auth and RBAC to all endpoints"
```

Update `ROADMAP.md`: move from `🚧 In Progress` → `✅ Completed`.

---

## Part 4: What a Completed Feature Folder Looks Like

```
docs/features/user-authentication/
├── README.md             ← Status: ✅ Complete — 2026-03-08
├── requirements.md       ← What was agreed to build
├── plan.md               ← All tasks checked [x], Status: ✅ Complete
└── verification.md       ← All checks passed, acceptance criteria met
```

This is your permanent record. Six months later you can open this folder and know exactly what was built, why, how, and whether it passed.

---

## Part 5: Your Complete File Structure

```
~/.claude/                              ← GLOBAL (all projects, all sessions)
├── CLAUDE.md                           ← Your personal preferences & universal rules
├── settings.json                       ← Global hooks (session start only)
└── commands/                           ← Global slash commands
    ├── start.md                        ← /start       — orient to any project
    ├── clarify.md                      ← /clarify     — agree on requirements
    ├── plan.md                         ← /plan        — create written plan
    ├── implement.md                    ← /implement   — execute plan task by task
    ├── verify.md                       ← /verify      — run all checks, save report
    └── new-project.md                  ← /new-project — bootstrap new project

your-project/                           ← PROJECT (this project only)
├── CLAUDE.md                           ← Project memory: stack, commands, rules
├── ROADMAP.md                          ← High-level backlog
├── docs/
│   └── features/                       ← ALL feature work tracked here
│       └── user-authentication/        ← one folder per feature
│           ├── README.md               ← master status + links
│           ├── requirements.md         ← agreed requirements
│           ├── plan.md                 ← plan + live task checklist
│           └── verification.md         ← verification report
└── .claude/
    ├── settings.json                   ← Language-specific hooks
    └── commands/                       ← Project-specific commands (optional)
```

---

## Quick Reference

| What | Command | When |
|---|---|---|
| Orient to project | `/start` | Start of every session |
| Agree on requirements | `/clarify <feature>` | Before planning |
| Create plan | `/plan <feature>` | After requirements agreed |
| Execute plan | `/implement <feature>` | After plan approved |
| Run verification | `/verify <feature>` | After implementation done |
| Bootstrap new project | `/new-project` | Once per new project |
| Switch to Plan Mode | `Shift+Tab` (twice) | Risky / multi-file changes |
| Stretch context window | `/compact` | Mid-task, context filling up |
| Start fresh between phases | `/clear` | Between phases |
| Resume after `/clear` | "Read docs/features/user-authentication/plan.md and continue from the first unchecked task" | Resuming implementation |

---

## The 5 Golden Rules

1. **Never let Claude write code without an approved written plan.** This prevents 80% of wasted effort.
2. **One folder per feature.** Everything about a feature — requirements, plan, verification — lives in `docs/features/<name>/`.
3. **Start a new session (`/clear`) between phases.** Clean context produces better results at each phase.
4. **Progress lives in files, not chat.** Update plan.md checkboxes continuously. Anything not in a file is lost when the session ends.
5. **You are the architect. Claude is the engineer.** Review plans before approving. Review diffs before committing.
```
