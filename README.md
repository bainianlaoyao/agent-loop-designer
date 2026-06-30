# Agent Loop Designer

[English README](README.en.md)

> 把智能体循环设计成平台无关的 Markdown 契约。只做设计，不执行循环。

这是一个用于 Codex / Claude Code 等智能体 CLI 的 skill。它把“做一个能循环完成 X 的智能体”转成一份 `loop.md`：里面明确目标、可观测成功信号、终止条件、进度不变量、领域匹配的验证方式、审批门，以及失败后的 residual routing。

这个 skill 只负责设计循环；真正执行循环的是你自己的智能体环境。

## 什么是 agent loop？

主流智能体框架最后都会收敛到一个形状：LLM 在一个 `while` 循环里调用工具，直到任务完成或触发停止条件。循环内部通常是 **Perceive → Reason → Plan → Act → Observe**，也就是 ReAct 的 Thought / Action / Observation。

问题在于：没有边界的循环会失控。曾经有记录显示，一个 agent 因为提示词写着“retry until you get data”，在工具损坏时 5 分钟内调用失败工具 **400 次**。agent loop 最难的不是让它跑起来，而是让它**正确停止**。

Agent Loop Designer 解决的就是这个设计问题。

## 和 open-dynamic-harness 的关系

这个项目继承了 [open-dynamic-harness](https://github.com/bainianlaoyao/open-dynamic-harness) 的工程控制思想：bounded recovery、domain-matched verification、residual classification、approval gates。

区别在于：

| | open-dynamic-harness | agent-loop-designer |
|---|---|---|
| 输出 | `workflow.py` | `loop.md` |
| 约束方式 | 程序控制流 | 文档契约 |
| 执行者 | runtime 读取文件执行 | 任意 agent 自驱执行 |
| 平台 | 路由到具体 CLI / model | 平台无关 |
| 范围 | 生成 + 执行 + review | 只设计 |

`open-dynamic-harness` 在代码里强制进度不变量；这个 skill 在 `loop.md` 中约定进度不变量，并要求执行 agent 每轮打印它。纪律相同，绑定方式不同。

## 使用方式

在你的 agent CLI 中调用：

```text
$agent-loop     # Codex
/agent-loop     # Claude Code
```

然后描述你希望 agent 循环处理的任务。这个 skill 会先调查当前平台可用的 subagent 能力，再逐条追问设计决策：成功信号、终止条件、进度不变量、不可逆动作、失败路由、是否需要 subagent、最坏情况和 Plan B。

每个问题都会带一个推荐答案供你接受或修正。设计树收敛后，它才会起草 `loop.md`，并和你迭代到可接受为止。

## 安装

最快的安装方式是使用跨 agent 的 [`skills`](https://github.com/vercel-labs/skills) CLI。它是第三方安装器，不是 OpenAI 或 Anthropic 官方安装器；但本仓库的 `skills/agent-loop/` 结构兼容它。

### Codex

全局安装：

```bash
npx skills add bainianlaoyao/agent-loop-designer --skill agent-loop --agent codex --global --yes
```

安装后重启 Codex，让它加载新 skill。

如果要安装到当前仓库，去掉 `--global`。

### Claude Code

全局安装：

```bash
npx skills add bainianlaoyao/agent-loop-designer --skill agent-loop --agent claude-code --global --yes
```

安装后重启 Claude Code，让它加载新 skill。

如果要安装到当前项目，去掉 `--global`。

安装前可以先预览安装器能发现哪些 skill：

```bash
npx skills add bainianlaoyao/agent-loop-designer --list
```

### 手动安装

复制整个 skill 目录，而不是只复制 `SKILL.md`：

```text
skills/agent-loop/
```

常见目标路径：

```text
~/.codex/skills/agent-loop/       # Codex 用户级 skill
~/.claude/skills/agent-loop/      # Claude Code 个人 skill
.agents/skills/agent-loop/        # Codex 仓库级 skill
.claude/skills/agent-loop/        # Claude Code 项目级 skill
```

安装后的目录必须包含 `SKILL.md`、`assets/` 和 `references/`。

## 示例

| 示例 | 展示内容 |
|---|---|
| [`simple-research.md`](examples/simple-research.md) | 单领域、可逆任务的最小四段式 loop |
| [`code-fix-with-approval.md`](examples/code-fix-with-approval.md) | 带审批门和 residual routing 的完整 loop |
| [`anti-example-unbounded.md`](examples/anti-example-unbounded.md) | 一个故意写坏的无界 loop，展示为什么它不可交付 |

## 友链

- [LINUX DO](https://linux.do/) - 新的理想型社区

## License

MIT
