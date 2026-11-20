# TermSmith

轻量级 Coding Agent，基于 ReAct 架构驱动 LLM 自主完成编程任务，五层分层架构支持多模型接入、MCP 工具扩展与多 Agent 协作。

## 功能特性

- **ReAct Agent 主循环**：思考 → 调用工具 → 观察结果 → 再思考，LLM 流式输出思考过程与执行轨迹，端到端完成从问题定位到修改验证的开发任务。
- **内置工具集**：文件读写与编辑、Glob / Grep 检索、Bash 执行、任务管理、Git Worktree 等。
- **MCP 工具扩展**：支持外接 MCP Server，工具描述采用延迟加载策略，避免工具数量较多时挤占上下文窗口。
- **上下文管理**：渐进式上下文压缩、大体量工具结果外置到磁盘、会话以 JSONL 持久化，支持长时间连续编程不丢失关键上下文。
- **记忆系统**：用户级 / 项目级指令文件分级加载，自动记忆提取与后台整理，实现跨会话的经验复用。
- **权限与安全**：规则引擎 + 危险命令识别 + 会话级授权 + OS 级沙箱（Linux bubblewrap / macOS Seatbelt），在全自动模式下仍保留安全边界。
- **多 Agent 协作**：SubAgent 并行分发、Git Worktree 文件级隔离、teammate 协作与验证 Agent，突破单 Agent 上下文窗口瓶颈。
- **可扩展机制**：Skill 技能包、自定义斜杠命令、Hooks 钩子（条件匹配与前置 / 后置执行器）。
- **交互方式**：Textual TUI、计划模式、文件历史与 `/rewind` 回退、Remote Web 模式。
- **多模型接入**：Anthropic / OpenAI / OpenAI 兼容协议，通过 providers 配置切换。

## 技术栈

Python 3.11+ · Textual · Anthropic SDK · OpenAI SDK · MCP · Pydantic · PyYAML · httpx · websockets

## 快速开始

需要 Python 3.11+ 与 [uv](https://docs.astral.sh/uv/)。

```bash
uv sync
```

在项目根目录新建 `.termsmith/config.yaml`（或用户级 `~/.termsmith/config.yaml`）：

```yaml
providers:
  - name: deepseek
    protocol: openai-compat
    base_url: https://api.deepseek.com
    api_key: ${DEEPSEEK_API_KEY}
    model: deepseek-chat

permission_mode: default
mcp_servers: []
```

`api_key` 支持 `${ENV_VAR}` 形式引用环境变量，避免密钥明文落盘。`config.yaml` 与 `.termsmith/` 已加入 `.gitignore`。

启动：

```bash
uv run termsmith                          # 交互 TUI
uv run termsmith -p "修复登录页的空指针"     # 单次任务，非交互执行
uv run termsmith --mode plan               # 计划模式
uv run termsmith --remote                 # Web 模式
```

> 配置按当前工作目录查找，请在项目根目录启动。

常用命令：`/help` `/compact` `/clear` `/memory` `/skill` `/mcp` `/plan` `/review` `/rewind` `/status` `/tasks` `/trace` `/worktree` `/sandbox` `/session`

## 项目结构

```
termsmith/
  agent.py / driver.py / conversation.py   Agent 主循环与会话管理
  prompts.py / config.py                  系统提示词与分层配置
  tools/                                   内置工具实现
  mcp/                                     MCP 客户端与延迟加载策略
  context/                                 上下文压缩与工具结果外置
  memory/                                  指令文件、自动记忆与后台整理
  permissions/ / sandbox/                   权限规则与 OS 级沙箱
  agents/ / teams/ / worktree/              多 Agent 协作与文件级隔离
  commands/ / hooks/ / skills/              斜杠命令、钩子与技能包
  app.py / remote.py                       TUI 与 Web 模式
tests/                                      pytest 用例
```

## 测试

```bash
uv run pytest -q
```
