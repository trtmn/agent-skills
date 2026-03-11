---
name: self-improvement
description: Enables Claude to continuously improve by logging corrections, errors, and learnings during conversations and promoting the most valuable insights to permanent project memory (CLAUDE.md). Use this skill whenever a user corrects Claude's behavior ("actually, it should be...", "don't do that"), requests a capability Claude lacked, Claude encounters a command failure or unexpected error, or after any non-trivial session where new patterns emerged. Also trigger when the user explicitly says "log this", "remember that", "add this to your memory", "learn from this", or asks Claude to analyze GitHub PRs/issues for lessons. The goal is to make every mistake a one-time mistake.
---

# Self-Improvement

This skill enables you to get better over time — by logging what you learn and promoting the best insights to places where they'll actually stick.

## Log Files

Maintain three structured markdown files in a `.learnings/` directory at the project root (create it if it doesn't exist):

- **`.learnings/LEARNINGS.md`** — Corrections, knowledge gaps, best practices
- **`.learnings/ERRORS.md`** — Command failures, exceptions, unexpected behaviors
- **`.learnings/FEATURE_REQUESTS.md`** — Capabilities the user asked for that you couldn't provide

## When to Log

Log immediately when you detect:
- **User corrections**: "Actually, it should be...", "No, don't...", "That's wrong because..."
- **Knowledge gaps**: You gave outdated info, missed a project convention, or had to be corrected
- **Command/tool failures**: A shell command failed, an API call errored, a file operation didn't work as expected
- **Feature requests**: The user asked for something you couldn't do or didn't know how to do
- **Patterns repeating**: Same mistake made more than once in a session

Log immediately — context is freshest right after the issue.

## Entry Format

### LEARNINGS.md
```markdown
## [LRN-YYYYMMDD-001] Short descriptive title
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
## [ERR-YYYYMMDD-001] Short descriptive title
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
## [FEAT-YYYYMMDD-001] Short descriptive title
- **Timestamp**: ISO-8601
- **Priority**: low | medium | high | critical
- **Status**: pending | implemented | promoted
- **Area**: (same categories)
- **Request**: What the user asked for
- **Context**: Why they needed it, what they were trying to accomplish
- **Workaround**: What was done instead, if anything
```

## Promotion Workflow

Not everything belongs in permanent memory — only promote insights that are broadly applicable, not session-specific. A good candidate for promotion is something that, if forgotten, would cause the same mistake again.

Promote to **CLAUDE.md** (project memory) when:
- A learning applies to the entire project and would affect future sessions
- A pattern has appeared multiple times
- A correction reflects a project convention that should always be followed

When promoting, add a concise entry to CLAUDE.md in the appropriate section, mark the source entry `status: promoted`, and note what was promoted.

Do **not** promote things that are:
- One-off workarounds for a temporary situation
- Specific to a single file or narrow context
- Already captured elsewhere (e.g., in existing docs or code comments)

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

## End-of-Session Review

At the end of a substantial session (or when the user asks), do a brief review:

1. Scan the log files for any `pending` entries that are high/critical priority
2. Assess whether any should be promoted to CLAUDE.md
3. Tell the user: "I logged N learnings this session. Here's what I think is worth keeping long-term: [summary]." Then ask if they want to promote any of them.

Keep this lightweight — don't turn every session into a ceremony. If nothing notable happened, say so and move on.

## Principles

**Be specific, not vague.** "Don't use deprecated APIs" is useless. "Don't use `os.path.join` in this codebase — it uses `pathlib.Path` everywhere (see utils.py)" is useful.

**Document the why.** Future Claude won't remember the context. Make the lesson self-contained enough that it makes sense without the original conversation.

**Don't over-log.** Not every stumble needs to be recorded. Use judgment — log things that are likely to recur or that reveal a genuine gap, not every minor uncertainty.

**Link related entries.** If an error connects to a learning or a feature request, cross-reference them.
