# aicoding-cookbook

这个仓库是一个「AI Coding 配置/排障小抄」集合，主要记录我在本机/团队环境里把工具链跑通的最小步骤、常见坑位和可复用文件。

## 主要内容

### augment-mcp-config/

Augment Context Engine MCP 一键配置工具包，专为 Claude Code AI 自动化配置设计。

| 文件 | 说明 |
|------|------|
| `Augment-MCP配置教程.md` | AI 执行指令文档，覆盖 Augment MCP + Codex MCP 配置（Windows / macOS / Linux） |
| `augment.mjs` | J3n5en 修改版，包含 `codebase-retrieval` + `prompt-enhancer` 工具 |

**使用方式**：将 `augment-mcp-config/` 文件夹拖拽给 Claude Code，告诉它你的 token，AI 会自动完成全部配置。

### skills/

可复用的 Codex/Agent 技能与脚本集合。

| 技能 | 说明 |
|------|------|
| `codex/skill-creator` | Skill 打包与初始化工具 |
| `codex/todo-list-csv` | TODO CSV 跟踪脚本 |

## 快速开始

### Augment MCP 配置

1. 将 `augment-mcp-config/` 文件夹拖拽到 Claude Code
2. 输入：`帮我配置 Augment MCP，token 是 ace_xxxxxx`
3. 等待 AI 自动完成配置和测试

### 使用 Codex Skills

1. 进入 `skills/codex/` 目录
2. 阅读对应 skill 的 `SKILL.md` 了解用法
3. 按说明在 Codex 中调用

## 安全提示

- 不要把任何真实 token/key 写进仓库、issue、截图或聊天记录
- 本仓库 `.gitignore` 已忽略常见敏感文件

## 致谢

- [J3n5en](https://github.com/J3n5en) - augment.mjs 修改版作者
- [Augment ACE MCP](https://acemcp.heroman.wtf/)
- [OpenAI Codex CLI](https://developers.openai.com/codex/cli)
