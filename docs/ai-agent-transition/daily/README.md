# 14 天日学习文档索引

这组文档是面向测试开发工程师的 14 天 Agent（智能体）实战学习材料。每一天都围绕同一个项目推进：

> CI 故障诊断与缺陷草稿 Agent

每天建议投入 2 到 3 小时，固定节奏如下：

```text
阅读和理解 30-40 分钟
编码实现 60-90 分钟
测试和复盘 20-30 分钟
```

## 使用规则

1. 按顺序学习，不要跳过第 1～6 天直接研究 Multi-Agent。
2. 每天必须留下代码、测试或数据集中的至少一种产出。
3. 每天至少准备一个成功样例和一个失败样例。
4. 当天没有完成“完成标准”时，不要急着进入下一天。
5. 参考资料只阅读当天对应的章节，不需要从头通读所有框架文档。

## 每日文档

- [第 1 天：最小 Agent 闭环与模型调用](./day-01-agent-basics.md)
- [第 2 天：Prompt 与结构化输出](./day-02-prompt-structured-output.md)
- [第 3 天：Tool Calling 与工具契约](./day-03-tool-calling.md)
- [第 4 天：工具可靠性、重试与幂等](./day-04-tool-reliability.md)
- [第 5 天：Workflow、State 与 Checkpoint](./day-05-workflow-state.md)
- [第 6 天：检索、RAG 与证据链](./day-06-retrieval-rag.md)
- [第 7 天：MCP 最小实践](./day-07-mcp.md)
- [第 8 天：Golden Dataset 与样本设计](./day-08-golden-dataset.md)
- [第 9 天：确定性自动化测试](./day-09-deterministic-testing.md)
- [第 10 天：Agent 行为评测](./day-10-agent-evaluation.md)
- [第 11 天：Trace 与可观测性](./day-11-tracing-observability.md)
- [第 12 天：Agent 安全测试](./day-12-agent-security.md)
- [第 13 天：端到端整合与演示](./day-13-e2e-demo.md)
- [第 14 天：项目收尾与面试表达](./day-14-finalize-interview.md)

## 项目资料

- [项目规格](../02-project-spec.md)
- [Agent 评测与测试方案](../03-agent-evaluation-and-testing.md)
- [面试准备与项目表达](../04-interview-prep.md)
- [学习记录模板](../05-learning-log.md)
- [资源地图](../06-resource-map.md)
- [Agent Harness 本地笔记](../../agent-engineering-react-to-agent-harness-summary.md)

## 统一项目目录

建议在仓库中创建一个独立目录保存练习代码：

```text
projects/ci-failure-agent/
├── app/
├── data/
├── evals/
├── tests/
├── traces/
├── README.md
└── pyproject.toml
```

如果你不想在当前仓库直接写代码，也可以把这个目录放在其他工作区，但每天文档中的文件名和命令保持一致，方便复盘。

## 进度表

| 天数 | 状态 | 关键产出 |
|---|---|---|
| 第 1 天 | TODO | 一次模型调用和结构化分类 |
| 第 2 天 | TODO | 输出 Schema 和校验测试 |
| 第 3 天 | TODO | 3 个只读 Tool |
| 第 4 天 | TODO | 超时、重试和幂等策略 |
| 第 5 天 | TODO | State Schema 和可恢复流程 |
| 第 6 天 | TODO | 检索工具和证据链 |
| 第 7 天 | TODO | 一个 MCP Tool 或 Server |
| 第 8 天 | TODO | 20～30 条 Golden Case |
| 第 9 天 | TODO | 15 条以上确定性测试 |
| 第 10 天 | TODO | 第一版评测报告 |
| 第 11 天 | TODO | 完整 Trace 和失败回放 |
| 第 12 天 | TODO | 安全测试矩阵 |
| 第 13 天 | TODO | 3 个端到端演示 |
| 第 14 天 | TODO | README、架构图和面试材料 |

