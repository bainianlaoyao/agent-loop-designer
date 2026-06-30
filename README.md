# Agent Loop Designer

> Design disciplined, platform-neutral agent loops as Markdown documents that
> any agent CLI can self-drive. Design only — never execute.

A single skill that turns "make an agent that does X" into a `loop.md`: a
portable loop specification with an observable success signal, a progress
invariant on every path, domain-matched verification, and narrow approval
gates. The skill designs the loop; your own agent runs it.

## What is an agent loop?

The form OpenAI, Anthropic, Google, Microsoft, Meta, and LangChain all
converged on: an LLM calls tools inside a `while` loop until the task completes
or a stop condition fires. Its inner cycle is **Perceive → Reason → Plan → Act
→ Observe** (ReAct's Thought / Action / Observation).

The catch: a loop without bounds fails. There is a documented case of an agent
calling a broken tool **400 times in five minutes** because its prompt said
"retry until you get data" and nothing made it stop. The hard part of an agent
loop is not making it run — it is making it **stop correctly**. That is a
design problem, and it is what this skill addresses.

## Relationship to open-dynamic-harness

This project takes the engineering-cybernetics discipline from
[open-dynamic-harness](https://github.com/bainianlaoyao/open-dynamic-harness)
— bounded recovery, domain-matched verification, residual classification,
approval gates — but applies it differently:

| | open-dynamic-harness | agent-loop-designer |
|---|---|---|
| Output | `workflow.py` | `loop.md` (Markdown) |
| Discipline enforced by | program control flow | document convention |
| Executor | a runtime walks the file | any agent self-drives the doc |
| Platform | routes to specific CLIs/models | platform-neutral by contract |
| Scope | generate + run + review | design only |

Where the harness *enforces* a progress invariant in code, this skill *agrees*
on one in prose and requires the executing agent to print it every iteration.
The discipline is the same; the binding is by convention, not by kernel.

## Usage

Invoke the skill in your agent CLI:

```text
$agent-loop     # Codex
/agent-loop     # Claude Code
```

Describe the task you want an agent to loop over. The skill **surveys the
subagent capability** of its current platform first (so it knows what it can
actually dispatch), then **grills you** — interrogating one branch of the
design tree at a time (success signal, termination, progress invariant,
irreversible actions, failure routing, subagent selection if the loop
dispatches any, worst case), each question shipping a recommended answer for
you to accept or reject — until the loop's shape is fully decided. Only then
does it draft `loop.md` and iterate with you to acceptance. Hand the
resulting `loop.md` to any agent to execute.

## Install

The quickest install path is the cross-agent
[`skills`](https://github.com/vercel-labs/skills) CLI. It is a third-party
installer, not an OpenAI or Anthropic official installer, but this repository's
`skills/agent-loop/` layout is compatible with it.

### Codex

Install globally:

```bash
npx skills add bainianlaoyao/agent-loop-designer --skill agent-loop --agent codex --global --yes
```

Then restart Codex so it picks up the new skill.

For a repo-local install, omit `--global`.

### Claude Code

Install globally:

```bash
npx skills add bainianlaoyao/agent-loop-designer --skill agent-loop --agent claude-code --global --yes
```

Then restart Claude Code so it picks up the new skill.

For a project-local install, omit `--global`.

To preview what the installer will find before installing:

```bash
npx skills add bainianlaoyao/agent-loop-designer --list
```

### Manual install

Copy the whole skill directory, not just `SKILL.md`:

```text
skills/agent-loop/
```

Common destinations:

```text
~/.codex/skills/agent-loop/       # Codex user skill
~/.claude/skills/agent-loop/      # Claude Code personal skill
.agents/skills/agent-loop/        # Codex repo skill
.claude/skills/agent-loop/        # Claude Code project skill
```

The installed directory must contain `SKILL.md`, `assets/`, and `references/`.

## Examples

| Example | Shows |
|---|---|
| [`simple-research.md`](examples/simple-research.md) | Minimal four-section loop for a single-domain, reversible task |
| [`code-fix-with-approval.md`](examples/code-fix-with-approval.md) | Full six-section loop with an approval gate and residual routing |
| [`anti-example-unbounded.md`](examples/anti-example-unbounded.md) | An intentionally broken loop — the 400-broken-calls shape — annotated with why it is undeliverable |

## License

MIT
