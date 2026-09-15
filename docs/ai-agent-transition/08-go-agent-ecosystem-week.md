# Go AI Agent 生态补充周

这是一份放在 14 天核心计划之后的补充计划，用 7 天左右梳理 Go 生态中的 AI Agent（智能体）开发框架和工程边界。

目标不是重新做一遍完整项目，而是回答三个问题：

1. Go 生态里有哪些可用的 Agent Framework（智能体框架）和 SDK？
2. 它们分别适合什么工程场景？
3. 如果你的主语言或目标岗位偏 Go，应该如何把前面 14 天学到的 Agent、Tool、State、RAG、MCP、Eval 和 Trace 迁移过去？

## 1. 学习定位

### 1.1 本周不替代前 14 天主线

前 14 天仍建议用 `OpenAI Agents SDK` 快速完成完整闭环。Go 补充周放在后面，原因是：

- Go 生态的 Agent Framework 选择更多，但稳定度和资料密度不如 Python 主线集中。
- 你需要先理解 Agent 工程问题，再比较不同语言和框架的实现差异。
- Go 更适合服务化、并发控制、接口隔离、观测和平台工程表达。

本周的核心成果是：

```text
Go Agent 生态地图
  + 一个 Eino 最小 Agent / Workflow Demo
  + 一个框架选型对比表
  + 一份面试表达材料
```

### 1.2 推荐主线

默认采用：

```text
Eino 主线
  -> OpenAI Go SDK 做底层模型调用理解
  -> MCP Go SDK 做协议和工具生态理解
  -> 其他框架做选型阅读
```

如果目标岗位明确，可以切换主线：

| 目标岗位或技术栈 | Go 生态主线 | 学习重点 |
|---|---|---|
| Go 后端 + Agent 应用 | `Eino` | Agent、Tool、Graph、Workflow、RAG、Trace |
| Google / Gemini / Vertex AI | `Google ADK for Go` | Agent、Tool、Session、Google Cloud 集成 |
| 腾讯 / tRPC / 企业服务化 | `tRPC-Agent-Go` | Session、Memory、RAG、MCP、OpenTelemetry |
| Azure / Microsoft 生态 | `Microsoft Agent Framework for Go` | Workflow、多 Agent、治理和观测 |
| 只想理解底层调用 | `OpenAI Go SDK` | Responses、Tool Calling、结构化输出 |
| 只想做 Tool 协议 | `MCP Go SDK` | MCP Client / Server、Tool Schema、权限边界 |

## 2. Go 生态框架地图

| 项目 | 定位 | 建议优先级 | 为什么看 |
|---|---|---:|---|
| [Eino](https://www.cloudwego.io/docs/eino/) | Go AI 应用和 Agent 开发框架 | P0 | Go 原生，覆盖 Agent、Tool、Graph、Workflow、RAG、Trace 等常见工程边界 |
| [Google ADK for Go](https://github.com/google/adk-go) | Google Agent Development Kit 的 Go 实现 | P1 | 适合 `Gemini`、`Vertex AI`、`Google Cloud` 岗位 |
| [tRPC-Agent-Go](https://trpc-group.github.io/trpc-agent-go/) | tRPC 生态的 Go Agent 框架 | P1 | 偏服务化，关注 Session、Memory、RAG、MCP、OpenTelemetry |
| [LangChainGo](https://github.com/tmc/langchaingo) | LangChain 的 Go 实现 | P2 | 适合理解 Chain、Agent、Tool、Retriever、Vector Store 的经典抽象 |
| [Genkit Go](https://genkit.dev/docs/go/get-started/) | AI App Framework 的 Go 版本 | P2 | 适合 Go 后端 AI 应用、RAG、Testing、Evaluation 和工具化场景 |
| [Microsoft Agent Framework for Go](https://github.com/microsoft/agent-framework-go) | Microsoft Agent Framework 的 Go SDK | P2 | 适合 Azure、Foundry、多 Agent Workflow 和企业治理方向 |
| [OpenAI Go SDK](https://github.com/openai/openai-go) | OpenAI 官方 Go SDK | P0 周边能力 | 是模型调用和 Tool Calling 的底层 SDK，不是完整 Agent Framework |
| [MCP Go SDK](https://github.com/modelcontextprotocol/go-sdk) | MCP 官方 Go SDK | P0 周边能力 | 用于写 MCP Client / Server，补足工具生态和协议理解 |

优先级解释：

- `P0`：本周建议动手或重点理解。
- `P1`：按目标岗位选择一个深入。
- `P2`：建立生态认知，不建议本周完整实战。

## 3. 本周最终交付物

建议在本地练习项目里增加一个 Go 子目录：

```text
projects/ci-failure-agent-go/
├── cmd/
│   └── ci-agent/
├── internal/
│   ├── agent/
│   ├── model/
│   ├── tools/
│   ├── workflow/
│   ├── rag/
│   └── eval/
├── data/
├── traces/
├── README.md
└── go.mod
```

一周结束时至少有：

- 一张 Go Agent 生态对比表。
- 一个可以运行的最小 Go Agent Demo。
- 2～3 个只读 Tool。
- 一个可测试的状态流转或 Workflow。
- 一组复用前 14 天 Golden Case 的 Go 测试。
- 一份 Go 生态选型说明。
- 一段面试回答：为什么这个场景选 Go / 不选 Go。

## 4. 每日学习计划

### 第 1 天：建立 Go Agent 生态地图

学习目标：

- 明确 Go 生态中 Agent Framework、LLM SDK、MCP SDK 的区别。
- 选出本周主线框架，默认选择 `Eino`。
- 把前 14 天的项目能力映射到 Go 生态。

怎么学：

1. 先阅读 `Eino` 官方首页和核心概念，不追 API 细节。
2. 快速浏览 `OpenAI Go SDK`，确认它是底层 SDK，不是完整 Agent Runtime。
3. 快速浏览 `MCP Go SDK`，确认它解决的是 Tool / Resource / Prompt 的协议接入。
4. 只看 `Google ADK for Go`、`tRPC-Agent-Go`、`LangChainGo`、`Genkit Go`、`Microsoft Agent Framework for Go` 的概览页。

动手任务：

- 建一个 `go-agent-ecosystem-matrix.md`，列出：
  - Framework / SDK 名称。
  - 是否支持 Agent。
  - 是否支持 Tool。
  - 是否支持 Workflow / Graph。
  - 是否支持 RAG / Memory。
  - 是否支持 MCP。
  - 是否支持 Trace / Observability。
  - 是否适合当前 CI 诊断项目。
- 写下本周主线选择：默认 `Eino`。

参考资料：

- [Eino](https://www.cloudwego.io/docs/eino/)
- [OpenAI Go SDK](https://github.com/openai/openai-go)
- [MCP Go SDK](https://github.com/modelcontextprotocol/go-sdk)
- [Google ADK for Go](https://github.com/google/adk-go)
- [tRPC-Agent-Go](https://trpc-group.github.io/trpc-agent-go/)
- [LangChainGo](https://github.com/tmc/langchaingo)
- [Genkit Go](https://genkit.dev/docs/go/get-started/)
- [Microsoft Agent Framework for Go](https://github.com/microsoft/agent-framework-go)

完成标准：

- 能用 3 句话解释 `Agent Framework`、`LLM SDK`、`MCP SDK` 的区别。
- 能说明为什么本周选择 `Eino` 作为 Go 主线。
- 能指出哪些框架只是了解，不纳入本周动手范围。

### 第 2 天：Go 中的模型调用和结构化输出

学习目标：

- 用 Go 表达 `FailureInput` 和 `DiagnosisResult`。
- 理解 Go 里结构化输出、JSON Schema、错误处理和 `context.Context` 的关系。
- 为模型调用留出 Mock 边界。

怎么学：

1. 回看第 1～2 天 Python 主线里的输入输出 Schema。
2. 用 Go struct 定义同样的输入和输出。
3. 通过接口抽象模型调用，不把 SDK 调用散落到业务逻辑里。
4. 用 Mock Model 先跑通，不依赖真实模型。

动手任务：

```go
type FailureInput struct {
    RunID      string `json:"run_id"`
    Project    string `json:"project"`
    FailedTest string `json:"failed_test"`
    Log        string `json:"log"`
}

type DiagnosisResult struct {
    Category         string   `json:"category"`
    Summary          string   `json:"summary"`
    Confidence       float64  `json:"confidence"`
    EvidenceIDs      []string `json:"evidence_ids"`
    NeedsHumanReview bool     `json:"needs_human_review"`
}
```

- 实现 `Classifier` 接口。
- 实现 `MockClassifier`。
- 用 `go test ./...` 验证分类、字段校验和异常输入。

参考资料：

- [OpenAI Go SDK](https://github.com/openai/openai-go)
- [Eino](https://www.cloudwego.io/docs/eino/)
- [Go testing](https://pkg.go.dev/testing)
- [Go context](https://pkg.go.dev/context)

完成标准：

- 不调用真实模型也能跑通分类流程。
- 所有模型输出必须被 Go struct 校验。
- 业务层不直接依赖具体 LLM SDK。

### 第 3 天：Tool Calling 和工具契约

学习目标：

- 用 Go 接口表达 Tool Contract（工具契约）。
- 把前 14 天的日志、代码和 Diff 查询工具迁移成 Go 版本。
- 明确 `context timeout`、错误类型、只读权限和参数校验。

怎么学：

1. 先不追复杂 Agent API，手写 Tool 接口，理解边界。
2. 再看 `Eino` 如何封装 Tool。
3. 对比 Go 的显式错误返回和 Python 异常处理差异。

动手任务：

- 实现 3 个只读工具：
  - `SearchLogTool`
  - `SearchCodeTool`
  - `SearchDiffTool`
- 每个工具至少包含：
  - 输入 struct。
  - 输出 struct。
  - 参数校验。
  - 超时控制。
  - 错误分类。
  - 单元测试。

建议接口：

```go
type Tool[I any, O any] interface {
    Name() string
    Run(ctx context.Context, input I) (O, error)
}
```

参考资料：

- [Eino](https://www.cloudwego.io/docs/eino/)
- [OpenAI Go SDK](https://github.com/openai/openai-go)
- [Go errors](https://pkg.go.dev/errors)
- [Go context](https://pkg.go.dev/context)

完成标准：

- 3 个 Tool 都有单元测试。
- Tool 不允许任意路径读取。
- Tool 超时和错误不会直接击穿主流程。

### 第 4 天：Workflow、State 和 Graph

学习目标：

- 理解 Go Agent 框架里 `Workflow`、`Graph`、`State` 的实现方式。
- 用 `Eino` 或手写状态机表达 CI 诊断流程。
- 复用前 14 天的 `State` 思维：不要从最终文本推断运行事实。

怎么学：

1. 先画出状态流转。
2. 再决定用 `Eino Graph / Workflow`，还是先手写轻量状态机。
3. 重点看每一步的输入、输出、失败和恢复点。

建议状态：

```text
CREATED
CLASSIFYING
COLLECTING_EVIDENCE
DIAGNOSING
DRAFTING_BUG
WAITING_APPROVAL
COMPLETED
FAILED
```

动手任务：

- 实现 `RunState`。
- 实现状态转移校验。
- 把第 3 天的 Tool 接进流程。
- 至少模拟一次 `WAITING_APPROVAL`。
- 保存一份 JSON Trace 或运行日志。

参考资料：

- [Eino](https://www.cloudwego.io/docs/eino/)
- [tRPC-Agent-Go](https://trpc-group.github.io/trpc-agent-go/)
- [Go encoding/json](https://pkg.go.dev/encoding/json)

完成标准：

- 能从日志看出每一步状态变化。
- 非法状态转移会被拒绝。
- 审批是状态，不只是终端提示。

### 第 5 天：RAG、Memory 和 MCP

学习目标：

- 理解 Go 生态里 RAG、Memory、MCP 的常见组合。
- 实现一个最小检索工具或 MCP Tool。
- 区分“检索内容”和“系统指令”。

怎么学：

1. 回看第 6～7 天 Python 主线中的 RAG 和 MCP。
2. 用 Go 实现一个最小关键词检索。
3. 如果时间够，用 `MCP Go SDK` 写一个只读 Tool Server。
4. 只了解 `Eino`、`tRPC-Agent-Go`、`Genkit Go` 对 RAG / MCP 的支持方式。

动手任务：

- 实现 `Evidence` struct。
- 从本地 `data/logs` 或 `data/code` 检索证据。
- 输出必须引用真实存在的 `evidence_id`。
- 可选：实现一个 MCP Server，暴露 `search_log` Tool。

参考资料：

- [MCP Go SDK](https://github.com/modelcontextprotocol/go-sdk)
- [Model Context Protocol Introduction](https://modelcontextprotocol.io/introduction)
- [Eino](https://www.cloudwego.io/docs/eino/)
- [tRPC-Agent-Go](https://trpc-group.github.io/trpc-agent-go/)
- [Genkit Go](https://genkit.dev/docs/go/get-started/)

完成标准：

- 至少 5 条证据可被检索。
- 输出中不存在的 `evidence_id` 会被测试发现。
- 能解释 MCP 是工具协议，不是完整权限系统。

### 第 6 天：测试、评测、Trace 和安全

学习目标：

- 用 Go 的测试体系验证 Agent 周边逻辑。
- 把前 14 天的 Golden Case 复用到 Go Demo。
- 理解 Go 服务化 Agent 的观测、安全和权限边界。

怎么学：

1. 用 `go test` 覆盖 Tool、State、Schema 和 Policy。
2. 用 JSONL 复用前面的 Golden Case。
3. 为每次运行输出结构化 Trace。
4. 补最小安全测试：路径越权、Prompt Injection、危险动作审批。

动手任务：

- 新增 `internal/eval`。
- 写一个读取 JSONL 的评测命令。
- 输出：
  - 分类准确率。
  - Tool 选择准确率。
  - 证据引用准确率。
  - 安全拦截结果。
- 为 Trace 加 `run_id`、`step`、`tool_name`、`duration_ms` 和 `error`。

参考资料：

- [Go testing](https://pkg.go.dev/testing)
- [OpenTelemetry Go](https://opentelemetry.io/docs/languages/go/)
- [tRPC-Agent-Go](https://trpc-group.github.io/trpc-agent-go/)
- [Genkit Go](https://genkit.dev/docs/go/get-started/)

完成标准：

- `go test ./...` 通过。
- 至少 10 条 Golden Case 可跑评测。
- Trace 能定位一次失败发生在哪个步骤。

### 第 7 天：收尾、选型报告和面试表达

学习目标：

- 把 Go 生态梳理成可讲清楚的工程判断。
- 输出一个能放到面试中的 Go Agent 对比结论。
- 明确下一步要深挖哪个框架。

怎么学：

1. 回看这一周的生态矩阵、代码 Demo、测试和 Trace。
2. 对照前 14 天的 Python 主线，比较 Go 和 Python 的实现差异。
3. 准备 90 秒面试表达。

动手任务：

- 完成 `projects/ci-failure-agent-go/README.md`。
- 完成 `go-agent-ecosystem-matrix.md`。
- 写一段框架选型总结：

```text
我会优先用 Eino 梳理 Go 生态里的 Agent、Tool、Workflow、RAG 和 Trace，
因为它更贴近 Go 服务端工程。OpenAI Go SDK 适合作为底层模型调用能力，
MCP Go SDK 适合工具协议接入。Google ADK for Go、tRPC-Agent-Go、
Genkit Go 和 Microsoft Agent Framework for Go 则按目标岗位和云生态选择。
```

完成标准：

- 能说明 Go 生态中至少 5 个项目的定位差异。
- 能运行一个 Go Agent / Workflow Demo。
- 能用测试开发视角讲清楚 Tool、State、Eval 和 Trace 的质量边界。
- 能回答“为什么不直接用 Python 主线框架”的问题。

## 5. 本周不要做

- 不要同时完整学习所有 Go Agent Framework。
- 不要把 Python 项目完整重写成 Go。
- 不要第一天就接真实 CI、数据库、向量库和缺陷系统。
- 不要只看框架示例，不写 Tool、State、Eval 和 Trace。
- 不要忽略 Mock，真实模型不稳定时会影响你判断工程结构。

## 6. 面试表达模板

可以这样讲：

> 我前面先用 Python 和 OpenAI Agents SDK 完成了 Agent 主流程，是为了快速覆盖 Tool Calling、结构化输出、RAG、MCP、Eval、Trace 和安全边界。之后我用一周梳理 Go 生态，重点看了 Eino、Google ADK for Go、tRPC-Agent-Go、LangChainGo、Genkit Go、Microsoft Agent Framework for Go、OpenAI Go SDK 和 MCP Go SDK。我的判断是：如果做 Go 服务端 Agent 应用，Eino 更适合做主线；OpenAI Go SDK 适合底层模型调用；MCP Go SDK 适合工具协议；其他框架按公司技术栈和云生态选择。框架不是核心，核心是 Tool 权限、State 恢复、评测、Trace 和可观测性。

## 7. 官方参考入口

- [Eino](https://www.cloudwego.io/docs/eino/)
- [Google ADK for Go](https://github.com/google/adk-go)
- [tRPC-Agent-Go](https://trpc-group.github.io/trpc-agent-go/)
- [LangChainGo](https://github.com/tmc/langchaingo)
- [Genkit Go](https://genkit.dev/docs/go/get-started/)
- [Microsoft Agent Framework for Go](https://github.com/microsoft/agent-framework-go)
- [OpenAI Go SDK](https://github.com/openai/openai-go)
- [MCP Go SDK](https://github.com/modelcontextprotocol/go-sdk)
- [Model Context Protocol Introduction](https://modelcontextprotocol.io/introduction)
- [Go testing](https://pkg.go.dev/testing)
- [Go context](https://pkg.go.dev/context)
- [OpenTelemetry Go](https://opentelemetry.io/docs/languages/go/)
