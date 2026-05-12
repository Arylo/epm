---
name: codebase-analyzer
description: "Run the full codebase-analyzer workflow by delegating to the `codebase-analyzer` agent with `all`. Use when: the user wants the complete pipeline in one step, including repository scanning, Jira story search, user journey mapping, test case generation, and end-user guide generation; 运行完整 codebase-analyzer 流水线。"
---

# Codebase Analyzer Skill

When this skill is invoked, delegate immediately to the `codebase-analyzer` agent.

## Invocation Rules

1. Use the `codebase-analyzer` agent.
2. Pass `all` as the stage selection so the agent runs the full pipeline.
3. If the user included a focus area or extra constraints, append them after `all` in the same request.
4. Treat `all` as the user's answer to the agent's stage-selection step. Do not ask the user to choose stages again unless they explicitly override `all`.

## Prompt Template

Use this prompt when delegating:

```text
all
```

If the user supplies extra scope, use:

```text
all

Additional focus:
- <user focus or constraints>
```

After the agent finishes, summarize the generated outputs and point the user to the `output/` artifacts when they exist.
