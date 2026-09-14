# 资源地图（Resource Map）

这份资源列表按“当前任务需要什么”组织，不建议一次性从头读完。

## 1. 本地已有资料

- [Agent 工程思考：从 ReAct 到 Agent Harness](../agent-engineering-react-to-agent-harness-summary.md)

重点阅读：

- ReAct 和 Agent Runtime（智能体运行时）的边界。
- State（状态）、View（视图）、Control（控制）的区别。
- Approval（审批）为什么是可恢复暂停点。
- Artifact（产物）和子 Agent 为什么需要稳定引用。
- UI（用户界面）为什么不能自己猜测运行事实。

这些内容对应本路线的第 5 天、第 11 天和项目中的 State Schema。

## 2. Agent 基础和工具调用

- [OpenAI Agents SDK Python 官方文档（Python 智能体开发工具包）](https://openai.github.io/openai-agents-python/)
- [OpenAI Agents SDK Tracing 官方文档（运行追踪）](https://openai.github.io/openai-agents-python/tracing/)
- [OpenAI Agents SDK Testing 官方文档（测试）](https://openai.github.io/openai-agents-python/testing/)

阅读目标：

- Agent（智能体）、Tool（工具）、Handoff（交接）、Guardrail（安全护栏）的基本使用方式。
- 如何记录一次 Run（运行）。
- 如何测试工具和 Workflow（工作流）。

阅读边界：

- 不需要第一天就通读整个 SDK。
- 先围绕最小工具调用和 Trace 找示例。

## 3. MCP

- [Model Context Protocol Introduction](https://modelcontextprotocol.io/introduction)
- [Model Context Protocol Specification](https://modelcontextprotocol.io/specification)

阅读目标：

- Tools（工具）、Resources（资源）、Prompts（提示模板）的职责。
- MCP Client（客户端）和 Server（服务端）的关系。
- 工具发现和参数 Schema（结构约束）。
- MCP 不负责替代业务权限、认证和审计。

短期只需要完成一个 Tool（工具）的接入，不需要把协议规范全部背下来。

## 4. RAG 和证据

重点不是先选向量数据库，而是理解：

- 文档如何切分。
- 查询如何召回。
- 结果如何排序。
- 证据如何保留来源。
- 生成结果如何引用证据。
- 检索结果如何防止注入。

第一版可以按这个顺序：

```text
关键词检索
  -> SQLite FTS 或本地索引
  -> 向量检索
  -> 重排和混合检索
```

如果项目数据量很小，关键词检索已经足够支撑面试 Demo（演示）。

## 5. 评测和质量

建议优先搜索和阅读这些主题：

- 大语言模型应用评测（LLM application evaluation）。
- Agent 轨迹评测（Agent trajectory evaluation）。
- Golden Dataset（黄金数据集）。
- LLM-as-Judge（大模型评审）。
- Prompt 回归测试（Prompt regression testing）。
- Tool Calling 评测（Tool calling evaluation）。
- 可观测性与追踪（Observability and tracing）。

阅读时始终带着项目问题：

- 这个方法能检测哪类失败？
- 需要什么标注数据？
- 结果能否重复？
- 能否定位到具体 Trace？
- 适不适合作为 CI 门禁？

## 6. 安全

建议了解：

- 提示词注入（Prompt Injection）。
- 间接提示词注入（Indirect Prompt Injection）。
- 过度代理权限（Excessive Agency）。
- 敏感信息泄露（Sensitive Information Disclosure）。
- 不当输出处理（Improper Output Handling）。
- 工具授权（Tool Authorization）。
- 沙箱和命令执行隔离。

安全学习的最低落地要求：

- 不执行任意 Shell（命令解释器）。
- Tool（工具）使用 allowlist（允许列表）。
- 读写权限分级。
- 高风险动作需要审批。
- 对路径、项目和用户范围校验。
- 对敏感信息脱敏。
- 保存审计 Trace（审计轨迹）。

## 7. 工程查漏补缺

只在项目需要时补下面内容：

| 遇到的问题 | 再补的知识 |
|---|---|
| 模型请求阻塞 | Python async（异步）、超时和取消 |
| 输出解析困难 | Pydantic、JSON Schema（JSON 结构约束） |
| 状态丢失 | SQLite、事务、checkpoint（检查点） |
| 工具重复执行 | 幂等键、重试和状态机 |
| 运行难以排查 | 结构化日志、Trace（运行轨迹）、关联 ID |
| 评测难以自动化 | pytest、JSONL、报告生成 |
| 本地环境不一致 | Docker、配置和依赖锁定 |

## 8. 框架选择建议

短周期内不要同时深入多个框架。选择标准：

- 官方文档能快速跑通。
- 工具调用和结构化输出简单。
- 能记录 Trace。
- 能被测试代码控制。
- 面试岗位中有一定出现频率。

如果项目目标是快速完成：

- 先选一个轻量 Agent SDK（智能体开发工具包）。
- Workflow（工作流）复杂后再考虑状态图框架。
- MCP 单独做最小接入。
- 把框架代码包在自己的 `Agent`、`Tool`、`State` 和 `Eval` 边界内。

面试时最重要的不是背框架 API（应用程序接口），而是能解释：

```text
模型负责什么
程序控制什么
工具允许什么
状态保存什么
评测验证什么
失败如何恢复
```
