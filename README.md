# aicoding-cookbook

这个仓库是一个「AI Coding 配置/排障小抄」集合，主要记录我在本机/团队环境里把工具链跑通的最小步骤、常见坑位和可复用文件。

## 主要内容

- `augment mcp setting/`
  - `augment-mcp-configuration-guide.md`：Augment Context Engine MCP（Codex / Claude Code）配置与验证指南（含 `prompt-enhancer` 不出现时的 `augment.mjs` 替换修复步骤，覆盖 Windows / Linux / macOS）。
  - `augment.mjs`：用于排障时替换本机 `@augmentcode/auggie` 的同名文件，确保 MCP 工具（包含 `prompt-enhancer`）可正常暴露。
  - `key.txt.example`：本机密钥文件模板（`key.txt` 会被 `.gitignore` 忽略）。
- `skills/`：一些可复用的 Codex/Agent 技能与脚本（例如 TODO CSV 跟踪、skill 打包等）。

## 快速开始（以 Augment MCP 为例）

1) 进入 `augment mcp setting/`，复制 `key.txt.example` 为 `key.txt` 并填入你的 token/url（不要提交）。
2) 按 `augment mcp setting/augment-mcp-configuration-guide.md` 配置 MCP 并验证工具可用性。
3) 若 `prompt-enhancer` 不出现，按指南替换本机 `@augmentcode/auggie/augment.mjs` 后重启 MCP。

## 安全提示

- 不要把任何真实 token/key 写进仓库、issue、截图或聊天记录。
- 本仓库默认忽略 `augment mcp setting/key.txt`；只提交 `key.txt.example` 模板。
