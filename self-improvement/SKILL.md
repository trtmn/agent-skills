---
name: self-improvement
description: Run the self-improvement agent to review this session and the ~/.learnings/ log files. Use this skill whenever the user explicitly asks to review learnings, promote entries to CLAUDE.md, do an end-of-session review, or analyze GitHub PRs/issues for recurring patterns. Also use when the user says "promote", "review learnings", "what have we learned", or "self-improvement". Do NOT use this skill just for logging — logging happens automatically without the skill (see Passive Logging below). This skill is specifically for the *review and promotion* workflow.
---

# Self-Improvement

This skill has two modes:

1. **Passive logging** (always active, no skill invocation needed) — Every error, correction, suggestion, or learning gets logged to `~/.learnings/` immediately as it happens. This is baked into Claude's behavior via CLAUDE.md and does not require triggering this skill.

2. **Active review and promotion** (triggered by `/self-improvement`) — Review accumulated learnings, promote valuable ones to CLAUDE.md, analyze GitHub PRs/issues, and do end-of-session retrospectives. Promotion **only** happens here, never automatically.

## Log Files

Maintain three structured markdown files in `~/.learnings/` (create it if it doesn't exist):

- **`~/.learnings/LEARNINGS.md`** — Corrections, knowledge gaps, best practices, suggestions
- **`~/.learnings/ERRORS.md`** — Command failures, exceptions, unexpected behaviors
- **`~/.learnings/FEATURE_REQUESTS.md`** — Capabilities the user asked for that you couldn't provide
- **`~/.learnings/CHANGELOG.md`** — Permanent, append-only record of every promotion (and reversion) to CLAUDE.md
- **`~/.learnings/ARCHIVE.md`** — All reviewed entries (promoted or skipped) move here. Not loaded into context unless the user explicitly asks to see it.

## Passive Logging (Always Active)

Every error, correction, suggestion, or learning gets logged immediately — no exceptions, no judgment calls about whether it's "worth it." The whole point is to build a complete record. Future sessions can filter; this session's job is to capture.

Log immediately — don't ask permission, don't wait for a pause. Context is freshest right after the event. After logging, briefly tell the user what you logged (one line, e.g., "Logged that port conflict to ERRORS.md"). Don't belabor it.

### What triggers a log entry

- **User corrections**: "Actually, it should be...", "No, don't...", "That's wrong because..." → LEARNINGS.md
- **Knowledge gaps**: You gave outdated info, missed a project convention, or had to be corrected → LEARNINGS.md
- **Suggestions**: User suggests a better approach, tool, or pattern → LEARNINGS.md
- **Command/tool failures**: A shell command failed, an API call errored, a file operation didn't work as expected → ERRORS.md
- **Feature requests**: The user asked for something you couldn't do → FEATURE_REQUESTS.md
- **Patterns repeating**: Same mistake made more than once in a session → LEARNINGS.md (bump to high priority)

### What does NOT need a log entry

- Typos or trivial self-corrections that won't recur
- Failures caused by transient conditions (network blip, server momentarily down)
- Things already documented in the project's CLAUDE.md with the exact same detail

## Entry Format

Each entry ID uses a 6-character random hex hash (e.g., `a3f7c1`) to avoid collisions across sessions and projects. Generate the hash at log time — any method works (random, truncated SHA, etc.) as long as it's 6 hex characters.

### LEARNINGS.md
```markdown
## [LRN-a3f7c1] Short descriptive title
- **Timestamp**: ISO-8601
- **Priority**: low | medium | high | critical
- **Status**: pending | promoted
- **Area**: frontend | backend | infra | tests | docs | config | workflow | other
- **What happened**: Brief description of the correction or gap
- **Lesson**: The concrete takeaway
- **Source**: e.g., "User correction in conversation", "PR #42", "Issue #17"
- **Suggested fix**: Specific action or rule change
```

### ERRORS.md
```markdown
## [ERR-b2e9d4] Short descriptive title
- **Timestamp**: ISO-8601
- **Priority**: low | medium | high | critical
- **Status**: pending | resolved
- **Area**: (same categories as above)
- **Command/action**: What was attempted
- **Error**: The failure message or description
- **Root cause**: Why it failed (once known)
- **Fix**: What resolved it or what to do differently next time
- **Reproduction**: Steps to reproduce (if useful)
```

### FEATURE_REQUESTS.md
```markdown
## [FEAT-c8d2a5] Short descriptive title
- **Timestamp**: ISO-8601
- **Priority**: low | medium | high | critical
- **Status**: pending | implemented | promoted
- **Area**: (same categories)
- **Request**: What the user asked for
- **Context**: Why they needed it, what they were trying to accomplish
- **Workaround**: What was done instead, if anything
```

## Promotion (Only via `/self-improvement`)

Promotion means writing an insight into a CLAUDE.md file so it persists across all future conversations. Promotion **never happens automatically** — it only happens when the user explicitly runs `/self-improvement` and reviews what should be promoted.

This is intentional: auto-promotion leads to CLAUDE.md bloat and unwanted entries. The user should always be in the loop for what becomes permanent guidance.

### When the user runs `/self-improvement`

1. Read all pending entries from `~/.learnings/` (status: pending)
2. Present a summary of candidates worth promoting, grouped by theme
3. For each candidate, explain why it might deserve promotion and where it would go
4. Wait for the user to approve, reject, or modify each one
5. Only promote what the user explicitly approves
6. **Archive all reviewed entries** — both promoted and skipped entries get moved to `~/.learnings/ARCHIVE.md` and removed from their source file (see Archiving below)

### Promotion criteria (for making recommendations)

Suggest promoting entries that are:
1. **Broadly applicable** — applies beyond one conversation or file
2. **Likely to recur** — future Claude would plausibly hit the same issue
3. **Not already documented** — check the target CLAUDE.md first

### Where to promote

**Project CLAUDE.md** (in the project root) — for project-specific conventions, patterns, and pitfalls.

**User CLAUDE.md** (`~/.claude/CLAUDE.md`) — for cross-project knowledge that applies regardless of which project.

Rule of thumb: "Would this matter in a completely different project?" If yes → user CLAUDE.md. If no → project CLAUDE.md.

### How to promote (after user approval)

1. Read the target CLAUDE.md to find the right section (or create one if needed)
2. Write a concise, self-contained entry — it should make sense without the original conversation
3. Append an entry to `~/.learnings/CHANGELOG.md` (see format below)
4. Archive the source entry (see Archiving below)
5. Tell the user what you promoted and where

### Archiving reviewed entries

Every entry that gets reviewed during `/self-improvement` — whether promoted or skipped — gets archived. This keeps the active learnings files lean (only unreviewed entries) while preserving a complete history.

1. **Move** the full entry from its source file (LEARNINGS.md, ERRORS.md, or FEATURE_REQUESTS.md) to `~/.learnings/ARCHIVE.md`
2. Add a `- **Disposition**: promoted to <target> | skipped (<reason>)` field to the archived entry
3. **Remove** the entry from the source file

The archive is append-only and is **never loaded into context** unless the user explicitly asks (e.g., "show me the archive", "what did we skip last time", "search the archive for X"). This keeps it from eating up context window.

### Promotion Changelog

Every promotion gets recorded in `~/.learnings/CHANGELOG.md` — a permanent, append-only audit trail.

```markdown
## [PROMO-f4a8e2] Short title matching the source entry
- **Timestamp**: ISO-8601
- **Source**: LRN-a3f7c1 (or ERR-, FEAT- reference)
- **Target**: project CLAUDE.md | user CLAUDE.md (~/.claude/CLAUDE.md)
- **Section**: Which section of the target file it was added to
- **What was promoted**: The exact text or a close summary of what was written
- **Why**: One sentence on why this deserved promotion
```

Never edit or delete changelog entries — if a promotion turns out to be wrong, add a new `[REVERT-<hex>]` entry explaining what was removed and why, then remove it from the target CLAUDE.md.

Keep promoted entries lean. Distill to one or two sentences with a concrete example if helpful.

### What NOT to promote

- One-off workarounds for temporary situations
- Things specific to a single file or narrow context
- Debugging steps that are already in the fix (the code is the documentation)
- Anything already captured in the target CLAUDE.md (check first!)

## Learning from GitHub PRs and Issues

When the user asks you to learn from GitHub history (PRs, issues, discussions), use `gh` CLI to analyze patterns:

```bash
# Fetch closed PRs with review comments
gh pr list --state closed --limit 50 --json number,title,reviews,comments

# Look at a specific PR's review feedback
gh pr view <number> --json reviews,comments,body
```

Extract:
- Recurring reviewer complaints → lessons for LEARNINGS.md
- Rejected approaches (and why) → patterns to avoid
- Common bug patterns from issues → entries for ERRORS.md
- Frequently requested features → FEATURE_REQUESTS.md

Focus on patterns that appear in multiple PRs/issues rather than one-off events. Always cite the source PR or issue number in the entry.

## Principles

**Log everything, promote selectively.** Every error, correction, suggestion, and learning gets logged — no filtering, no judgment calls about importance. The log is a complete record. Promotion to CLAUDE.md is the selective step, and that only happens when the user asks for it.

**Be specific, not vague.** "Don't use deprecated APIs" is useless. "Don't use `os.path.join` in this codebase — it uses `pathlib.Path` everywhere (see utils.py)" is useful.

**Document the why.** Future Claude won't remember the context. Make the lesson self-contained enough that it makes sense without the original conversation.

**Link related entries.** If an error connects to a learning or a feature request, cross-reference them.

**Log without asking, promote with permission.** Logging is automatic and frictionless — just do it and briefly tell the user. Promotion requires explicit user approval via `/self-improvement`.
