# epam/scripts

这个仓库用于维护一组 GitHub Copilot 自定义 agent，面向代码库分析、用户旅程建模、测试文档生成和终端用户文档生成等场景。

当前内容全部位于 .github/agents 目录下，适合作为一个轻量的 agent 配置仓库使用。

## 目录结构

```text
.
└── .github/
    └── agents/
        ├── business-analyst.agent.md
        ├── codebase-analyzer.agent.md
        ├── end-user-guide-generator.agent.md
        ├── jira-story-searcher.agent.md
        ├── output-validator.agent.md
        ├── repo-scanner.agent.md
        ├── test-case-generator.agent.md
        └── user-journey-mapper.agent.md
```

## Agent 说明

| Agent | 作用 |
| --- | --- |
| business-analyst | 结合代码库扫描结果评估业务需求是否可行，并输出后续澄清问题。 |
| codebase-analyzer | 编排完整分析流水线，协调扫描、Jira 故事搜索、旅程、测试文档和用户指南生成。 |
| jira-story-searcher | 检测 Jira MCP 工具并搜索相关 Jira 故事，支持关键词搜索和全量获取两种模式。 |
| repo-scanner | 扫描仓库结构、配置、源码与测试，输出依赖与能力报告，并按 git commit/stage 哈希缓存到 output 目录。 |
| user-journey-mapper | 根据代码结构和依赖关系生成用户旅程地图。 |
| test-case-generator | 基于用户旅程生成结构化测试用例文档。 |
| end-user-guide-generator | 面向最终用户生成非技术操作指南。 |
| output-validator | 对生成结果做存在性和完整性校验，供流水线自动重试使用。 |

## 流水线关系

默认的主流程如下：

```text
(repo-scanner ∥ jira-story-searcher)
  -> user-journey-mapper
  -> test-case-generator
  -> end-user-guide-generator
```

其中 Stage 1 并行运行 repo-scanner 和 jira-story-searcher；jira-story-searcher 会自动检测 Jira MCP 工具是否可用，可用时获取所有 Jira 故事。如果未检测到 Jira 工具，流水线继续正常执行（Jira 为可选依赖）。

其中 output-validator 会在生成阶段后执行校验，保证产物满足预期格式与完整性要求。

## 使用方式

1. 在支持 GitHub Copilot 自定义 agent 的环境中打开该仓库。
2. 确保 agent 定义文件位于 .github/agents 目录。
3. 通过 Copilot Chat 调用相应 agent，按需执行单个 agent 或完整分析流水线。

常见用法：

- 使用 codebase-analyzer 生成完整分析产物。
- 使用 business-analyst 先判断某个业务需求在当前代码库中是否可行。
- 使用 repo-scanner 单独获取代码结构与依赖报告。

## 生成产物

按当前 agent 设计，分析结果通常输出到缓存目录或 output 目录，例如：

- .tmp/repo/{hash1}.md
- output/user-journey-map.md
- output/test-doc/
- output/end-user-guide.md

其中 `.tmp/repo/{hash1}.md` 为 repo-scanner 的依赖报告缓存：

- `hash1` 是当前 `git rev-parse HEAD` 的 commit hash。
- 若同名文件已存在且 `hash1` 匹配，repo-scanner 会直接复用该文件，不重新扫描。

这些文件不是仓库当前已有内容，而是由 agent 在执行过程中生成或复用。

## 适用场景

- 评估业务需求与现有代码能力的匹配度
- 从现有代码自动整理用户流程
- 生成测试设计文档
- 为最终用户整理使用说明

## 维护建议

- 新增 agent 时，优先保持单一职责，并在 frontmatter 中声明清晰的 tools 和 agents。
- 如果 agent 存在调用链，保持上游输出格式稳定，避免影响下游 agent。
- 修改校验规则时，同步检查相关生成 agent 的输出格式是否仍然兼容。
