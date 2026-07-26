---
name: practical-tdd
description: Use when implementing small features, fixing bugs, or adding functionality — brainstorm first, write test first, iterate with code review, version control every checkpoint
---

# Practical TDD — Full Cycle Workflow

## Overview

Every feature goes through: brainstorming → test → RED → GREEN → code review → user acceptance → commit. If bugs emerge during testing, fix first, then re-accept. If it's a new feature idea, loop back to brainstorming.

**Core principle:** Understand before you build. Test before you code. Commit after every checkpoint.

## The Full Cycle

```
  NEW FEATURE
      │
      ▼
  BRAINSTORMING (for complex features)
  "What exactly do you want?"
      │
      ▼
  Write test → user approves
      │
      ▼
  Subagent run → RED (must fail)
      │
      ▼
  Write minimal code
      │
      ▼
  Subagent run → GREEN (must pass)
      │
      ▼
  Subagent CODE REVIEW
      │
      ▼
  User decides
   │
   ├── Fix bugs → code review → new acceptance → user verifies
   │
   ├── Optimization suggested → discuss → TDD implement → new acceptance
   │
   ├── New feature idea → back to BRAINSTORMING
   │
   └── Done → COMMIT
```

## Step Details

### Step 0: Brainstorming (for complex or unclear features)

**Trigger:** The user's request is vague ("make a game", "add login"), involves multiple parts, or the user says "I'm not sure what I want."

Ask one question at a time. Understand the real need before writing any code.

For simple, clear requests (e.g., "fix the timer bug"), skip brainstorming and go straight to Step 1.

### Step 1: Write the test FIRST

Summarize the feature in 1-2 sentences. Get confirmation.

Write ONLY the test code. Present:

> "Here's the test — does this capture what you want?"

**GATE: User must approve the test.**

### Step 2: Subagent runs test (RED)

Dispatch a subagent with ONLY the test file and run command.

Test MUST fail. Report the failure.

### Step 3: Write minimal code

Smallest code to make the test pass. Nothing extra.

### Step 4: Subagent runs test (GREEN)

Fresh subagent, run tests. MUST pass.

### Step 5: Code Review with `ocr` (Alibaba Open Code Review)

Run the [Alibaba Open Code Review](https://github.com/alibaba/open-code-review) CLI (`ocr`) for AI-powered code review:

```bash
ocr review --diff          # Review staged/unstaged changes
ocr review --commit HEAD   # Review latest commit
ocr scan                   # Full project scan
```

Review must cover: bugs, edge cases, code quality, security, simpler approaches.

If `ocr` is unavailable, fall back to a fresh subagent review.

See `AGENTS.md` → CLI 工具 → `ocr` 代码审查 for full configuration and usage guide.

### Step 6: User decides

Present: test + code + review findings.

> "Test passes. Review found: [summary]. Ready?"

If user says:
- **"Looks good"** → go to Step 7 (acceptance)
- **"Fix X"** → fix, re-review, new acceptance
- **"Actually I also want Y"** → if complex, go to Step 0 (brainstorming); if simple, go to Step 1

### Step 7: Acceptance checklist

Create a NEW file for each acceptance round:

```
docs/superpowers/acceptance/<feature>-v<N>-YYYY-MM-DD.md
```

Increment version number each round (v1, v2, v3...).

**Only include unchecked items.** Already-verified items from previous rounds do NOT appear again.

Each item must include a "How to verify" instruction with exact steps (commands, URLs, browser actions). The user must be able to copy-paste and verify without thinking.

Each item must ALSO include a "Why this test" explanation — in plain language, explain WHAT we're really testing and WHY this verification method proves it. This is NOT a technical justification — it's so the user understands the testing logic and can spot if you're testing the wrong thing or using a weak method.

Example:
```
- [ ] **分布均匀性**
  > **Why:** 首点不踩雷是重随机逻辑自动保证的，不需要测。真正要测的是重新布雷后每个格子被选为雷的概率是否一致。跑 2000 局统计频率，如果某个格子雷特别多或特别少，说明随机分布被破坏了。
  > **How:** 终端运行 `node src/minesweeper.test.js`，确认 8/8 pass
```

**Every acceptance file MUST include a "How it works" section** — a plain-language explanation of the implementation logic. Explain the approach, the key data structures, the algorithm, in terms anyone can understand. This is NOT code documentation — it's for the user to understand what was built.

Template:

```markdown
# Acceptance: <feature> (v<N>)

**Date:** YYYY-MM-DD
**Tests:** N passing

## How it works

<!-- Plain-language explanation of the implementation logic. Simple enough for anyone to understand. -->

## Pending Items

- [ ] **<item>** — Expected: <what should happen>
  > **Why:** <plain-language explanation of testing logic — what are we really testing and why this method proves it>
  > **How to verify:** <step-by-step instructions, commands, URLs>

## User Notes

<!-- Bug reports, issues, new ideas go here -->
```

If the user previously reported bugs or issues in the notes section, address them BEFORE generating a new acceptance checklist.

### Step 8: COMMIT

After user confirms all items are checked, git commit with a descriptive message:

```
git add -A
git commit -m "<type>: <description>"
```

Types: `feat:`, `fix:`, `refactor:`, `test:`

### Bug Fix Loop (during acceptance testing)

When the user finds a bug during acceptance testing and writes it in the User Notes:

1. Read the user's notes
2. Fix the bug (go to Step 3: write minimal code, run tests, code review)
3. Generate a NEW acceptance file (increment version, e.g., `-v2`)
4. Only include the NEW/FIXED items — don't re-list already-passed items
5. The user's previous notes stay in the old file as history

### New Feature During Testing Loop

When the user has a new feature idea during testing:

1. If it's complex → go to Step 0 (brainstorming)
2. If it's simple → go to Step 1 (write test)
3. Create a SEPARATE acceptance file for the new feature

### Optimization Loop (user suggests a better approach)

When the user says "不应该这样做，应该那样做" during acceptance:
- This is NOT a bug — it's an improvement to the approach
- Discuss briefly to confirm the new approach
- Go to Step 1 (write/adjust the test)
- Example: "不该搬雷，该重随机" → write Test 8 (uniformity) → implement `randomizeMines` → new acceptance v2

### User Modifies Files Directly

When the user says "我已修改文件"/"查收":
- **Re-read** the acceptance file — the user may have checked boxes, added comments, or changed the checklist
- Do NOT assume the file content matches what you last wrote
- After reading, act on any new bugs, ideas, or notes in User Notes

### First Commit (git init)

Before the FIRST commit in a new project:
1. Check `git status` — if "not a git repository", run `git init`
2. Create `.gitignore` if missing — at minimum exclude: `node_modules`, `.env`, any file with API keys
3. Only THEN proceed with `git add -A` and `git commit`

### Test Scope Corrections

When the user says "你不应该测X，应该测Y":
- They are correcting the test's WHAT, not questioning the HOW
- Go back to Step 1: adjust the test to match the correct scope
- Example: "不该测不踩雷（逻辑保证）, 该测均匀性" → rewrite test → RED → GREEN → commit

## Version Control Rules

- **Commit after every GREEN phase** (all tests pass)
- **Commit after every accepted checklist**
- Use descriptive messages: `fix: relocate mine on first click`
- User should be able to `git log` and see every checkpoint

## Housekeeping (MANDATORY)

**After ANY code change that affects a running server, BEFORE asking the user to test:**

1. Kill the old server process
2. Start a fresh server
3. Verify the page loads without errors (check console)
4. ONLY THEN present to the user

```
# KILL old server first, THEN start new one — never skip the kill step
Stop-Process -Name python -Force -ErrorAction SilentlyContinue
Start-Sleep 1
Start-Process python -ArgumentList "-m", "http.server", "8080" -WindowStyle Hidden ...
```

**Never ask the user to test a stale server. This is the single most common failure mode.**

If the user says "你忘重启了", you have failed this rule. Apologize, restart immediately, and present the new verification.

## Why Subagents?

- **Test subagent**: clean context, no bias, sees only raw output
- **Code review subagent**: fresh eyes, catches what the author missed

## Common Mistakes

- **Skipping brainstorming for vague requests** — unclear feature = wasted code
- **Writing code before test** — delete and start over
- **Skipping RED phase** — untested test is not a test
- **Skipping code review** — tired eyes miss obvious bugs
- **Re-listing already-passed items** — only show unchecked items
- **No commit after checkpoint** — user can't track progress
- **Forgetting to restart server** — user tests stale code
- **Skipping git init on new project** — must initialize and add .gitignore before first commit
- **Not re-reading acceptance file after user says "查收"** — user may have modified it
- **Arguing with user's test scope correction** — if user says "测X不对,该测Y", adjust the test
- **Acceptance item without "Why this test"** — user must understand the testing logic, or they can't judge whether the verification is valid

## Red Flags

- "This is too simple to brainstorm/review"
- "I'll add the test after the code"
- "Let me just commit everything at the end"
- "The user won't notice the server wasn't restarted"
- "I'll re-list all items for completeness"
- "The user's improvement suggestion is just a minor detail"
- "I remember what the acceptance file said, no need to re-read"
- "The test scope doesn't matter, the implementation is correct"

**All of these mean: STOP.**
