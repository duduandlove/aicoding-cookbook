# Augment Context Engine MCP（Claude Code / Codex）最小可用配置（Relay 优先）

> 安全约定：不要把真实密钥写进仓库/截图发群。本仓库使用 `augment mcp setting/key.txt` 存放本机密钥（已在 `.gitignore` 忽略），模板见 `key.txt.example`。

本仓库已内置 `augment mcp setting/augment.mjs`（用于配置/排障时替换本机 `auggie` 的同名文件）：

- GitHub（查看）：https://github.com/lili-luo/aicoding-cookbook/blob/main/augment%20mcp%20setting/augment.mjs
- GitHub（raw）：https://raw.githubusercontent.com/lili-luo/aicoding-cookbook/main/augment%20mcp%20setting/augment.mjs

## 0) 准备 `key.txt`（本机，不要提交）

在 `<WORKSPACE_ROOT>/augment mcp setting/` 目录：

- 复制 `key.txt.example` → `key.txt`
- 编辑 `key.txt`，填入两行：

```text
AUGMENT_API_TOKEN=<RELAY_KEY_OR_ACCESS_TOKEN>
AUGMENT_API_URL=<RELAY_BASE_URL_OR_TENANT_URL>
```

> 如果 `AUGMENT_API_URL` **带路径**（例如 `https://example.com/relay`），必须以 `/` 结尾：`https://example.com/relay/`。

PowerShell：把 `key.txt` 导入当前 shell 的环境变量（不回显）：

```powershell
cd "<WORKSPACE_ROOT>\\augment mcp setting"
$kv = Get-Content .\key.txt | Where-Object { $_ -match '=' -and $_ -notmatch '^\s*#' }
foreach ($line in $kv) {
  $name, $value = $line -split '=', 2
  Set-Item -Path ("Env:" + $name.Trim()) -Value $value.Trim()
}
```

你还需要两项运行参数（给 agent 用）：

```text
WORKSPACE_ROOT=<ABS_PATH_TO_GIT_REPO_ROOT>
AUGGIE_CACHE_DIR=<ABS_PATH_TO_CACHE_DIR>   # 建议：~/.augment-mcp（或 %USERPROFILE%\.augment-mcp）
```

目标工具（MCP）：
- `mcp__augment-context-engine__prompt-enhancer`
- `mcp__augment-context-engine__codebase-retrieval`

## 0.5) 修复 `prompt-enhancer` 工具不出现（替换本机 auggie 的 `augment.mjs`）

现象：你在 MCP 的 tools 列表里只看到 `codebase-retrieval`，但没有 `prompt-enhancer`。

解决思路：找到你机器上 `auggie` 实际调用的 `@augmentcode/auggie/augment.mjs`，备份后用本仓库提供的 `augment mcp setting/augment.mjs` 覆盖，然后重启 MCP。

### Windows PowerShell

1) 找到 `auggie` 入口脚本位置：

```powershell
$auggie = Get-Command auggie
$auggie.Source
```

2) 计算 `augment.mjs` 目标路径（多数情况下是这个）：

```powershell
$basedir = Split-Path $auggie.Source -Parent
$target = Join-Path $basedir 'node_modules\@augmentcode\auggie\augment.mjs'
Test-Path $target
```

如果 `Test-Path` 为 `False`：打开 `$auggie.Source`，搜索 `augment.mjs`，以脚本里实际引用的路径为准。

3) 备份 + 覆盖（优先用仓库本地文件）：

```powershell
$bak = $target + '.bak.' + (Get-Date -Format 'yyyyMMddHHmmss')
Copy-Item $target $bak -Force
Copy-Item "<WORKSPACE_ROOT>\\augment mcp setting\\augment.mjs" $target -Force
```

也可以用 raw 地址下载覆盖（不需要本地仓库文件）：

```powershell
Invoke-WebRequest -Uri "https://raw.githubusercontent.com/lili-luo/aicoding-cookbook/main/augment%20mcp%20setting/augment.mjs" -OutFile $target
```

4) 重启 MCP / 重启 Codex CLI 或 Claude Code，然后按第 5 节验证 `prompt-enhancer` 是否可用。

> 注意：升级/重装 `@augmentcode/auggie` 可能会覆盖你替换的文件；如果 `prompt-enhancer` 又消失，重复本节即可。

### Linux / macOS（bash / zsh）

1) 找到本机 `augment.mjs` 的目标路径（npm 全局安装的常见位置）：

```bash
target="$(npm root -g)/@augmentcode/auggie/augment.mjs"
test -f "$target" && echo "$target"
```

如果 `test -f` 失败：先找到 `auggie` 入口脚本，然后看它实际引用了哪个 `augment.mjs`：

```bash
command -v auggie
grep -n "augment.mjs" "$(command -v auggie)" || true
```

2) 备份 + 覆盖（优先用仓库本地文件）：

```bash
cp "$target" "$target.bak.$(date +%Y%m%d%H%M%S)"
cp "<WORKSPACE_ROOT>/augment mcp setting/augment.mjs" "$target"
```

如果提示权限不足（例如安装在 `/usr/local/lib/node_modules`）：在 `cp` 前加 `sudo`，或用 `nvm` 安装 Node/npm（避免系统目录写权限问题）。

3) 也可以用 raw 地址下载覆盖（不需要本地仓库文件）：

```bash
curl -fsSL "https://raw.githubusercontent.com/lili-luo/aicoding-cookbook/main/augment%20mcp%20setting/augment.mjs" -o "$target"
# 或：wget -O "$target" "https://raw.githubusercontent.com/lili-luo/aicoding-cookbook/main/augment%20mcp%20setting/augment.mjs"
```

4) 重启 MCP / 重启 Codex CLI 或 Claude Code，然后按第 5 节验证 `prompt-enhancer` 是否可用。

---

## 核心规则（别踩坑）

1) **Relay 模式不需要 `auggie login`**：先 `auggie logout`，然后只看第 5 节验证是否真的能调用 MCP 工具（能让 relay 后端看到请求才算通）。
2) **不要用 `auggie token print` 判断 relay 可用性**：登出后它继续提示未登录是正常现象。
3) **必须在仓库根目录运行**（或启动 MCP 时传 `-w "<WORKSPACE_ROOT>"`），否则 `codebase-retrieval` 可能找不到/不索引。

---

## 1) 给 Claude Code / Codex Agent 的自动执行步骤

你只需要向主人确认：

- `WORKSPACE_ROOT`（仓库根目录绝对路径）
- `key.txt` 已填好（或当前 shell 已设置 `AUGMENT_API_TOKEN/AUGMENT_API_URL`）

按顺序自动执行：

1) Relay 模式：确保登出（避免 OAuth 会话干扰路由）

```bash
command -v auggie >/dev/null && auggie logout || true
```

2) 进入仓库根目录（非常关键）

```bash
cd "<WORKSPACE_ROOT>"
git rev-parse --show-toplevel
```

> WSL2：建议在 WSL2 里运行 `claude/codex/auggie`，并使用 Linux 路径（如 `/mnt/c/Users/<you>/repo`）。

3) 依赖检查（缺什么装什么）

```bash
node -v
npm -v
auggie --version
```

如果 `auggie` 不存在：

```bash
npm install -g @augmentcode/auggie@prerelease
```

4) 选择一个客户端完成 MCP 配置（第 3 节 / 第 4 节）
5) 立刻跑第 5 节验证

---

## 2)（可选）官方 OAuth 获取 token/url（非 relay 才需要）

```bash
auggie login
auggie token print
```

把输出 JSON 的：
- `accessToken` 作为 `AUGMENT_API_TOKEN`
- `tenantURL` 作为 `AUGMENT_API_URL`

---

## 3) Claude Code：一条命令配置 MCP（推荐 user scope）

> 这会把 token 写进 Claude 的 MCP 配置（不进仓库，但仍属于落盘密钥）。

```bash
claude mcp add --scope user --transport stdio augment-context-engine \
  --env AUGMENT_API_TOKEN="$AUGMENT_API_TOKEN" \
  --env AUGMENT_API_URL="$AUGMENT_API_URL" \
  -- auggie -w "<WORKSPACE_ROOT>" --augment-cache-dir "<AUGGIE_CACHE_DIR>" --mcp
```

Windows PowerShell（换行用反引号）：

```powershell
claude mcp add --scope user --transport stdio augment-context-engine `
  --env "AUGMENT_API_TOKEN=$env:AUGMENT_API_TOKEN" `
  --env "AUGMENT_API_URL=$env:AUGMENT_API_URL" `
  -- auggie -w "<WORKSPACE_ROOT>" --augment-cache-dir "<AUGGIE_CACHE_DIR>" --mcp
```

---

## 4) Codex CLI：一条命令配置 MCP

> 这会把 token 写进 `~/.codex/config.toml`（不进仓库，但仍属于落盘密钥）。

```bash
codex mcp add augment-context-engine \
  --env AUGMENT_API_TOKEN="$AUGMENT_API_TOKEN" \
  --env AUGMENT_API_URL="$AUGMENT_API_URL" \
  -- auggie -w "<WORKSPACE_ROOT>" --augment-cache-dir "<AUGGIE_CACHE_DIR>" --mcp
```

Windows PowerShell（换行用反引号）：

```powershell
codex mcp add augment-context-engine `
  --env "AUGMENT_API_TOKEN=$env:AUGMENT_API_TOKEN" `
  --env "AUGMENT_API_URL=$env:AUGMENT_API_URL" `
  -- auggie -w "<WORKSPACE_ROOT>" --augment-cache-dir "<AUGGIE_CACHE_DIR>" --mcp
```

---

## 5) 验证（以此为准）

Claude Code：

```bash
claude mcp list
claude mcp get augment-context-engine
cd "<WORKSPACE_ROOT>"
claude --print "Use the Augment codebase retrieval tool (mcp__augment-context-engine__codebase-retrieval) to find the repository root README file path. If you cannot access the tool, say: TOOL_UNAVAILABLE."
claude --print "Use the Augment prompt enhancer tool (mcp__augment-context-engine__prompt-enhancer) to rewrite this request: 帮我给这个项目加登录功能"
```

Codex CLI：

```bash
codex mcp list
codex mcp get augment-context-engine
codex exec -C "<WORKSPACE_ROOT>" "Use the Augment codebase retrieval tool (mcp__augment-context-engine__codebase-retrieval) to find the repository root README file path. If you cannot access the tool, say: TOOL_UNAVAILABLE."
codex exec -C "<WORKSPACE_ROOT>" "Use the Augment prompt enhancer tool (mcp__augment-context-engine__prompt-enhancer) to rewrite this request: 帮我给这个项目加登录功能"
```

---

## 6) 排障（只保留最高频）

1) 一直等/60s 超时（`deadline has elapsed`）
- Relay 模式先确认 `auggie logout`
- 用 `claude mcp get ...` / `codex mcp get ...` 确认 env 真的传进了 `auggie --mcp`
- `AUGMENT_API_URL` 带路径必须以 `/` 结尾

2) `Tool codebase-retrieval not found.`
- 确认你在 `WORKSPACE_ROOT`（git 根目录）
- 确认启动 `auggie --mcp` 时传了 `-w "<WORKSPACE_ROOT>"`

3) `auggie token print` 仍提示未登录
- Relay 模式：正常，忽略；只看第 5 节验证

4) `auggie: command not found`

```bash
npm install -g @augmentcode/auggie@prerelease
command -v auggie
```

PowerShell：

```powershell
npm install -g @augmentcode/auggie@prerelease
Get-Command auggie
```

5) `prompt-enhancer` 不出现
- 按第 0.5 节替换本机 `auggie` 的 `augment.mjs` 后重启 MCP

---

## 7) 安全提示

- `AUGMENT_API_TOKEN` 是密钥：别提交、别截图传播。
- 若不慎泄露：立刻吊销/换新，并更新 MCP 配置。

---

## 参考（官方）

- Augment：Claude Code Quickstart（Context Engine MCP）: https://docs.augmentcode.com/context-services/mcp/quickstart-claude-code
- Augment：安装 Auggie CLI: https://docs.augmentcode.com/cli/setup-auggie/install-auggie-cli
- Anthropic：Claude Code MCP: https://docs.anthropic.com/en/docs/claude-code/mcp
- OpenAI：Codex MCP: https://developers.openai.com/codex/mcp
