# Agent Skills Repository

## Overview
This repository contains a collection of **skills** compatible with the [skills.sh](https://skills.sh) specification and any AI agent that supports it. A *skill* is a reusable work‑flow that can be invoked from the command line, a Slack bot, or programmatically via the Claude Agent SDK. All the skills in this repo are designed to be portable, test‑driven, and idiomatic for anyone building AI‑assisted workflows.

> **Quick Start** – Visit [skills.sh](https://skills.sh) for the full skill directory and documentation.

## Repository Layout
```
├── README.md              👈 this file
├── LICENSE               📄 MIT license
├── packages/             📁 skill source trees
│   ├── keybindings-help/      # (example) keybinding skill
│   ├── obsidian-markdown/     # (example) markdown skill
│   └── …                      # (more skills as examples)
├── scripts/              📜 helper scripts (e.g., ci‑checks)
└── tests/                 🧪 unit tests for each skill
```
Each *package* follows the same conventions:
1.  `src/` – TypeScript source (or Python, depending on the skill).
2.  `package.json` – Declarative metadata that `skills` consumes.
3.  `bin/` – Executable entry point for the skill.
4.  `README.md` – Skill‑specific docs.

## Installation
Install any skill directly using `npx`:

```bash
npx skills add <username>/<reponame>
```

For example, to install a skill from this repository:

```bash
npx skills add anthropics/agent-skills
```

This automatically populates the local `.claude/skills/` directory and makes the skill available via the `skill` command.

## Usage
Skills are invoked with the global `skill` command: `skill <skill‑name> [args]`.  A few examples:

| Skill | Command | Description |
|-------|---------|-------------|
| `keybindings-help` | `skill keybindings-help --rebind ctrl+s` | Rebind the default Ctrl + S shortcut. |
| `obsidian-markdown` | `skill obsidian-markdown --file notes.md` | Render markdown with Obsidian callouts and wikilinks. |
| `xlsx` | `skill xlsx --open data.xlsx` | Open & manipulate spreadsheet data. |
| `find-skills` | `skill find-skills --query "compile time logging"` | Discover a new skill to install. |

Skills can also be composed in shell pipelines:
```bash
skill keybindings-help --list | skill find-skills --match "cli tool"
```

The `skills` binary exposes a CLI help command:
```bash
skill --help
skill keybindings-help --help
```

## Contribution Guide
We heavily rely on community contributions to grow the ecosystem.  Please follow these steps:

1.  Fork the repo.
2.  Create a new feature branch: `git checkout -b add-spell-check-skill`.
3.  Write the skill in the dedicated folder under `packages/`.
4.  Add a unit test in `tests/` and run `npm test`.
5.  Bump `package.json` `version` field.
6.  Open a PR against `main`.

All PRs must pass:
- Linting (`npm run lint`).
- Unit tests (`npm test`).
- Static type checking (`npm run typecheck`).
- No fatal `npm audit` warnings.

See [CONTRIBUTING.md](CONTRIBUTING.md) for detail.

## Standards & Quality
| Area | Tool |
|------|------|
| Linting | ESLint with the webpack style guide |
| Formatting | Prettier |
| Continuous Integration | GitHub Actions – `ci.yml` |
| Security Scanning | Trivy (container) + `npm audit` |
| Documentation | Typedoc for TypeScript skill crates |

## License
Unlicensed – public domain per the Unlicense (see LICENSE file).

---
For deeper documentation, visit the main [Claude Code docs](https://claude.ai/docs) or run `skill --help`.

