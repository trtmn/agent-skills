# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

Monorepo of reusable Claude Code skills compatible with the [skills.sh](https://skills.sh) specification. Each skill is a standalone workflow invocable from CLI, Slack, or the Claude Agent SDK. Distributed via `npx skills add trtmn/agent-skills`.

## Skill Directory Structure

Each skill lives in its own top-level kebab-case folder:

```
skill-name/
├── SKILL.md          # Required — YAML frontmatter (name, description, allowed-tools) + workflow docs
├── scripts/          # Executable scripts (bash or python)
├── references/       # Large reference docs loaded on-demand to minimize context
└── evals/            # Manual test prompts (gitignored)
```

## Key Conventions

- **SKILL.md is the contract** — defines triggers, tools, and the full workflow. YAML frontmatter must include `name`, `description`, and `allowed-tools`.
- **Scripts run standalone** — only their output enters Claude's context. Bash scripts use `set -e`, status to stderr, machine-readable JSON to stdout.
- **References stay separate** — large API docs, protocol specs, etc. go in `references/` to avoid bloating the skill definition.
- **No shared build step** — skills are distributed as-is from the repo.
- **No automated tests** — evals in `evals/` are manual (invoke + verify).

## Existing Skills

cowsay, font-extractor, home-assistant, unifi-api, pushover, tailscale-policy-manager, imsg, youtube-data-api, mastodon-cli, obsidian-cli, self-improvement, skills-manager, homebrew-dev, planka, pre-flight, and others.

## Files to Know

- `AGENTS.md` — AI agent guidance for creating and modifying skills
- `.claude/settings.json` — Base permissions
- `.claude/settings.local.json` — Expanded local permissions (tests, evals, CLI tools)
