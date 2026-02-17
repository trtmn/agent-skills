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
npx skills add trtmn/agent-skills
```




## License
see LICENSE file

