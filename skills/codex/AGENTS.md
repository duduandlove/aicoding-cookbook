# Global Agent Rules

You have personal skills stored in `~/.codex/skills/`.
Before starting a task, scan available skills.
If a skill matches, read its `SKILL.md` and follow it.
Announce which skill you are using.

## Official Documentation (Context7 + Web Search)
Whenever the task requires consulting official documentation, always retrieve it via the Context7 MCP and also do a web search to cross-check recency and fill any gaps:

## 默认使用中文与我沟通；除非我明确要求英文/其他语言。

1) Call `mcp__context7__resolve-library-id` to get the Context7-compatible library ID
2) Call `mcp__context7__get-library-docs` (use `topic` to focus the lookup)
3) Use web search (e.g., `web.run`) to confirm time-sensitive details (versions, deprecations, migration guides) and to find any missing official pages
4) If Context7 cannot resolve/find the docs, use web search to identify the correct official docs and then retry Context7 (or ask the user for an explicit Context7 library ID); if Context7 still has no coverage, proceed with the official web docs and note the limitation


## TODO CSV Tracking for Project Changes

When a task involves actual changes to the project (add/modify/delete files) and has multiple steps:

1) Call `update_plan` to create a plan (keep exactly 1 `in_progress`)
2) Create `"{Task Name} TO DO list.csv"` in the project root (`status` uses `TODO/IN_PROGRESS/DONE`, mapped 1:1 to plan `pending/in_progress/completed`)
3) On each progress update, sync both the CSV and plan; once all items are `DONE`, delete the CSV and mark all plan steps as `completed`

Tip: if the description matches, prefer the `todo-list-csv` skill (at `~/.codex/skills/todo-list-csv/`).
