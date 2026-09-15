# Agent 工程框架对比与选择

这份文档用于回答一个实际问题：

> 除了 OpenAI Agents SDK，还应该学习哪些 Agent Framework（智能体框架）？

结论不是把所有框架都学一遍，而是保留一条能快速交付的主线，再根据目标岗位选择一个替代框架做局部实践。

## 1. 先给结论

### 默认主线：OpenAI Agents SDK

本路线继续保留 [OpenAI Agents SDK](https://openai.github.io/openai-agents-python/) 作为默认实现，原因是：

- Python-first，学习成本低。
- 核心抽象较少，适合在两周内跑通最小闭环。
- `Agent`、`Tool`、`Handoff`、`Guardrail`、`Session`、`Tracing` 等能力覆盖本项目的主要需求。
- 可以把普通 Python 函数封装为带参数 Schema（结构约束）的 Tool。
- 现有第 1～14 天文档、项目规格和测试方案已经按这条主线组织。

如果还没有明确的目标岗位，建议完成下面这条路线：

```text
OpenAI Agents SDK 主线
  -> 第 5 天理解 LangGraph 的状态图思想
  -> 第 6 天理解 LlamaIndex 的数据和 RAG 思路
  -> 只选择一个框架做局部重写
```

### 最值得补充的替代框架：LangGraph

如果只能额外选择一个框架，优先选择 [LangGraph](https://docs.langchain.com/oss/python/langgraph/overview)。

它和当前项目的 `State`、`Workflow`、`Checkpoint`、`Approval`、失败恢复天然对应，适合展示以下能力：

- 显式定义节点、边和状态。
- 让流程分支可以被检查，而不是完全隐藏在模型循环里。
- 为暂停、恢复、人工介入和重试保留清晰的运行边界。
- 将“模型决策”和“程序控制的流程”拆开。

LangGraph 不需要替代整个 14 天项目。第 5 天只把分类、证据收集、诊断和审批几个节点重写成一张最小 State Graph（状态图）即可。

## 2. 五个框架如何分工

| Framework | 核心优势 | 更适合的岗位或项目 | 在本路线中的定位 | 优先级 |
|---|---|---|---|---|
| OpenAI Agents SDK | 快速构建 Tool Calling、Guardrail、Handoff 和 Trace | 通用 Agent 应用、快速交付、OpenAI 技术栈 | 默认主线，覆盖第 1～14 天 | 必学 |
| LangGraph | 显式 State Graph、持久化、可恢复执行和人工介入 | 长流程、审批、后台任务、复杂分支 | 第 5 天的替代实现 | 强烈推荐 |
| LlamaIndex | 数据连接、检索、RAG、Agent 和 Workflow | 知识库、文档问答、数据 Agent、RAG 平台 | 第 6 天的替代实现 | 按岗位选择 |
| PydanticAI | Python 类型安全、结构化输出、依赖注入和可测试性 | Python Agent、结构化业务应用、质量工程 | 第 1～4 天或第 9 天的替代实现 | 按岗位选择 |
| Google ADK | 面向 Agent 和 Tool 的代码式开发，适合 Google/Gemini 生态 | Gemini、Vertex AI、Google Cloud 相关岗位 | 第 1 天的可选预览或独立小 Demo | 有目标岗位再学 |

这里的“定位”是学习路线安排，不代表框架只能做表中的事情。多数框架都可以完成工具调用、结构化输出和多步流程，区别主要在于默认抽象、生态和工程体验。

## 3. 按能力选择，而不是按热度选择

### 3.1 想快速完成一个可展示项目

选择：

```text
OpenAI Agents SDK + pytest
```

重点展示：

- Tool Schema 和参数校验。
- Tool 失败、超时、重试和幂等。
- Golden Dataset 和行为评测。
- Trace、审批和安全边界。

这是最适合当前两周计划的默认方案。

### 3.2 想突出流程编排和后台任务能力

选择：

```text
OpenAI Agents SDK 主线 + LangGraph 第 5 天替代实现
```

只重写下面的部分：

```text
CLASSIFYING
  -> COLLECTING_EVIDENCE
  -> DIAGNOSING
  -> DRAFTING_BUG
  -> WAITING_APPROVAL
```

保持输入、工具结果、输出 Schema 和 Golden Case 不变，这样才能比较两个框架对同一业务问题的影响。

### 3.3 想进入 RAG、知识库或数据 Agent 方向

选择：

```text
OpenAI Agents SDK 主线 + LlamaIndex 第 6 天替代实现
```

重点不是学习所有数据连接器，而是理解：

- 文档如何进入索引。
- 检索结果如何保留来源。
- 证据如何进入 Agent 上下文。
- 检索内容为什么不能直接拥有系统指令权限。
- 评测如何区分召回失败、证据使用失败和生成失败。

### 3.4 想突出 Python 工程质量和类型约束

选择：

```text
PydanticAI + pytest
```

重点关注：

- `Pydantic` 模型如何约束输入和输出。
- 依赖如何注入 Mock 服务。
- 工具如何被单独测试。
- Agent 行为如何与确定性业务逻辑分离。

这条路线很适合把测试开发经验迁移到 Agent Quality（智能体质量）岗位。

### 3.5 目标岗位使用 Gemini 或 Google Cloud

选择：

```text
Google ADK + Gemini/Vertex AI 方向资料
```

只在职位描述明确出现 `Gemini`、`Vertex AI`、`Google Cloud` 或 `ADK` 时纳入核心学习。否则在两周内了解 Agent、Tool、Session 和评测入口即可，不要为了覆盖名词而重写整个项目。

## 4. 框架和当前项目的映射

| 项目边界 | OpenAI Agents SDK | LangGraph | LlamaIndex | PydanticAI | Google ADK |
|---|---|---|---|---|---|
| Agent 入口 | `Agent` + `Runner` | 节点中的模型调用 | Agent / Workflow | `Agent` | Agent |
| Tool | Function Tool | 节点或 Tool | Tool / Query Engine | Tool | Function Tool / Tool |
| 输出约束 | Pydantic / Schema | 由节点和模型输出共同约束 | Pydantic / Schema | Pydantic 原生体验 | Schema / 模型输出 |
| 流程状态 | Session、Run State 或应用层 State | State Graph、Checkpointer | Workflow Context | 应用层状态或依赖 | Session / Workflow 能力 |
| 人工审批 | Human-in-the-loop 能力和应用层控制 | 中断、恢复和人工介入 | 由 Workflow 或应用层实现 | 由应用层实现 | 由应用层或框架能力实现 |
| Trace / Eval | 内置 Tracing 和测试入口 | 通常结合 LangSmith 或自建 Trace | 结合 LlamaIndex 观测和自建评测 | Python 测试和自建评测 | ADK 评测和 Google 生态工具 |
| MCP | 有官方 MCP 相关入口 | 可通过工具或集成接入 | 可通过工具或集成接入 | 通过工具/集成接入 | 通过工具/集成接入 |

这张表只用于建立概念映射。不要把同一个能力的 API 名称当成通用标准，面试时应该优先讲清楚自己的 `Tool`、`State`、`Eval` 和权限边界。

## 5. 两周内的具体执行方式

### 方案 A：时间最紧

只完成：

1. OpenAI Agents SDK 的第 1～14 天主线。
2. 第 5 天阅读 LangGraph Overview，能画出状态图。
3. 第 6 天阅读 LlamaIndex Agent 入口，能说明 RAG 和 Agent 的关系。
4. 第 14 天准备一段框架选型说明。

### 方案 B：每天有 2～3 小时

在方案 A 的基础上，增加一次局部替代实现：

1. OpenAI Agents SDK 完成主线。
2. 用 LangGraph 重写第 5 天的 Workflow。
3. 复用同一批 Golden Case。
4. 记录两种实现的状态管理、错误恢复和测试差异。

### 方案 C：目标岗位偏 RAG

把第 6 天替换为：

1. 用 LlamaIndex 完成文档加载和最小检索。
2. 把检索结果转成统一的 `Evidence` Schema。
3. 用同一批样例验证证据引用。
4. 保留 OpenAI Agents SDK 的 Tool Calling 版本作为对照。

### 方案 D：目标岗位偏 Python Agent 测试

把第 1～2 天的结构化输出和第 9 天的测试部分，用 PydanticAI 做一个小版本：

1. 定义 `FailureInput` 和 `DiagnosisResult`。
2. 注入 Mock 的日志和代码检索服务。
3. 测试工具参数、结构化输出和依赖替换。
4. 对比 Agent 层测试和普通函数单测的边界。

## 6. 不建议在核心 14 天加入的内容

以下内容不是没有价值，而是和当前时间预算不匹配：

- 同时完整学习四到五个 Agent Framework。
- 为每个框架分别搭建一套项目。
- 先研究 Multi-Agent 拓扑，再处理单 Agent 的工具、状态和评测。
- 先搭建复杂向量数据库、消息队列或前端，再验证诊断闭环。
- 只背框架 API，不保留统一的输入、输出、Tool、State 和 Eval 契约。

建议把框架差异控制在一个可比较的实验中：

```text
相同输入
  -> 相同 Tool Schema
  -> 相同 State / Evidence 约束
  -> 相同 Golden Case
  -> 比较实现复杂度、可测试性、可恢复性和 Trace
```

## 7. Go 生态后续学习

Go 生态不建议塞进前 14 天主线。完成 Python 主线后，可以单独用一周梳理，见：[Go AI Agent 生态补充周](./08-go-agent-ecosystem-week.md)。

建议判断方式：

- `Eino`：作为 Go Agent 主线，重点看 Agent、Tool、Graph、Workflow、RAG 和 Trace。
- `OpenAI Go SDK`：作为底层模型调用 SDK，不等同于完整 Agent Framework。
- `MCP Go SDK`：作为 MCP Client / Server 和 Tool 协议能力，不等同于 Agent Runtime。
- `Google ADK for Go`、`tRPC-Agent-Go`、`Genkit Go`、`Microsoft Agent Framework for Go`：按目标岗位和云生态选择。
- `LangChainGo`：适合理解经典 Chain、Agent、Tool、Retriever 抽象，但不建议作为本周唯一主线。

面试中可以把 Go 生态作为“后续扩展能力”表达：同一套 `Tool`、`State`、`Eval`、`Trace` 边界可以迁移到 Go，只是具体框架和工程组织不同。

## 8. 面试中如何回答框架选型

可以用下面的结构回答：

> 我用 OpenAI Agents SDK 快速完成了单 Agent、Tool Calling、结构化输出、评测和 Trace 的主流程，因为它的抽象较少，适合快速验证业务闭环。对于需要显式状态、暂停恢复和人工审批的流程，我用 LangGraph 做过同一段 Workflow 的局部重写。对于 RAG 场景，我会重点比较 LlamaIndex 的数据和检索抽象；如果项目更重视 Python 类型安全和 Mock 测试，则会考虑 PydanticAI。最终选择不是看框架名称，而是看状态边界、工具权限、评测方式和运行可观测性。

## 9. 官方学习入口

- [OpenAI Agents SDK](https://openai.github.io/openai-agents-python/)
- [OpenAI Agents SDK Tools](https://openai.github.io/openai-agents-python/tools/)
- [OpenAI Agents SDK Testing](https://openai.github.io/openai-agents-python/testing/)
- [LangGraph Overview](https://docs.langchain.com/oss/python/langgraph/overview)
- [LlamaIndex Agents](https://developers.llamaindex.ai/python/framework/understanding/agent/)
- [PydanticAI Agents](https://ai.pydantic.dev/agents/)
- [Google Agent Development Kit](https://google.github.io/adk-docs/)
- [Eino](https://www.cloudwego.io/docs/eino/)
- [OpenAI Go SDK](https://github.com/openai/openai-go)
- [MCP Go SDK](https://github.com/modelcontextprotocol/go-sdk)
