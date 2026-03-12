# aicoding-cookbook

`aicoding-cookbook` is a practical repository of setup assets, reusable skills, and helper scripts for AI coding workflows. It is organized around real operational needs: configuring Augment MCP, reusing Codex skills, and standardizing task tracking or skill authoring routines.

This repository is not an application, SDK, or framework. It is a working cookbook for configuring tools and reusing workflow components in day-to-day AI-assisted development.

## Who This Repository Is For

- Developers who use AI coding tools such as Claude Code, Codex CLI, and Augment Context Engine.
- Operators who want ready-to-reuse configuration assets instead of rebuilding local setup steps from scratch.
- Teams or individuals who maintain repeatable Codex skills and task-tracking workflows.

## What Is Inside

### 1. Augment MCP configuration kit

The [`augment-mcp-config/`](./augment-mcp-config) directory contains a setup package for Augment Context Engine MCP:

- [`Augment-MCP配置教程.md`](./augment-mcp-config/Augment-MCP配置教程.md): a Chinese-language setup guide written for AI-assisted execution. It covers Augment MCP and Codex MCP setup across Windows, macOS, and Linux.
- [`augment.mjs`](./augment-mcp-config/augment.mjs): a bundled patched `augment.mjs` file referenced by the guide. The guide describes it as a modified Auggie entry file with `codebase-retrieval` and `prompt-enhancer` support.

This folder is aimed at users who want to hand a self-contained setup package to an AI assistant and let it perform the configuration steps.

### 2. Reusable Codex skills

The [`skills/codex/`](./skills/codex) directory currently contains three reusable skill packages:

- [`taskmaster`](./skills/codex/taskmaster): a multi-step task tracking skill with task specs, progress logging, and CSV-backed milestone management.
- [`todo-list-csv`](./skills/codex/todo-list-csv): a lightweight CSV task tracker that keeps a to-do file synchronized with agent planning.
- [`skill-creator`](./skills/codex/skill-creator): a guide and helper scripts for creating, validating, and packaging new skills.

### 3. Helper scripts and templates

The skills ship with actual scripts and templates, not just prose documentation:

- [`skills/codex/todo-list-csv/scripts/todo_csv.py`](./skills/codex/todo-list-csv/scripts/todo_csv.py): a Python CLI for creating and advancing task CSV files. The script includes commands such as `path`, `init`, `add`, `start`, `done`, `todo`, `advance`, `plan`, `status`, and `cleanup`.
- [`skills/codex/skill-creator/scripts/init_skill.py`](./skills/codex/skill-creator/scripts/init_skill.py): initializes a new skill directory from a template.
- [`skills/codex/skill-creator/scripts/package_skill.py`](./skills/codex/skill-creator/scripts/package_skill.py): validates and packages a skill directory into a zip archive.
- [`skills/codex/skill-creator/scripts/quick_validate.py`](./skills/codex/skill-creator/scripts/quick_validate.py): performs lightweight validation on a skill's `SKILL.md` metadata.
- [`skills/codex/taskmaster/assets/`](./skills/codex/taskmaster/assets): reusable templates for `SPEC.md`, `PROGRESS.md`, and `TODO.csv`.

## Repository Layout

```text
aicoding-cookbook/
├── README.md
├── augment-mcp-config/
│   ├── Augment-MCP配置教程.md
│   └── augment.mjs
└── skills/
    └── codex/
        ├── skill-creator/
        │   ├── LICENSE.txt
        │   ├── SKILL.md
        │   └── scripts/
        ├── taskmaster/
        │   ├── SKILL.md
        │   └── assets/
        └── todo-list-csv/
            ├── SKILL.md
            └── scripts/
```

## How To Use This Repository

### Use the Augment MCP package

1. Open [`augment-mcp-config/`](./augment-mcp-config).
2. Follow the instructions in [`Augment-MCP配置教程.md`](./augment-mcp-config/Augment-MCP配置教程.md).
3. Replace the installed `augment.mjs` only if your environment matches the assumptions in the guide.
4. Configure the MCP server in Claude Code or Codex CLI as described in the document.

### Reuse the Codex skills

1. Browse the folders under [`skills/codex/`](./skills/codex).
2. Read each `SKILL.md` to understand the trigger conditions and workflow.
3. Reuse the bundled scripts when you want the exact workflow captured in the skill instead of rebuilding it manually.

### Adapt the scripts directly

If you only need the automation pieces, inspect and reuse the bundled Python scripts directly:

- `todo_csv.py` for CSV-backed task progression
- `init_skill.py` for scaffolding a new skill
- `package_skill.py` for packaging a skill
- `quick_validate.py` for lightweight metadata checks

## Requirements and Assumptions

- Node.js and npm are expected for the Augment/Auggie-related setup flow.
- Python 3 is expected for the helper scripts under `skills/codex/`.
- The Augment setup guide assumes you are comfortable editing local tool configuration files such as `~/.claude.json` or `~/.codex/config.toml`.

## Language Notes

- This top-level README is written in English for broader GitHub readability.
- Some bundled documents are still Chinese-language source materials, especially [`augment-mcp-config/Augment-MCP配置教程.md`](./augment-mcp-config/Augment-MCP配置教程.md).
- The skills themselves are a mix of English and Chinese, depending on how they were originally authored.

## Security Notes

- Do not commit real API tokens, private keys, or machine-specific credentials into this repository.
- The current [`.gitignore`](./.gitignore) already excludes a local secret path: `augment mcp setting/key.txt`.

## Credits

- [J3n5en](https://github.com/J3n5en) for the modified `augment.mjs` referenced by the bundled guide.
- [Augment ACE MCP](https://acemcp.heroman.wtf/)
- [OpenAI Codex CLI](https://developers.openai.com/codex/cli)
