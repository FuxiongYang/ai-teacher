# 第 14 天：项目收尾与面试表达

## 今日目标

今天不再新增技术范围，而是把项目整理成求职作品：

> 能运行、能展示、能评测、能解释，并且能回答面试官对失败和安全的追问。

## 预计用时

| 环节 | 时间 |
|---|---:|
| 整理项目文档 | 50 分钟 |
| 整理评测和失败案例 | 40 分钟 |
| 准备简历和 90 秒介绍 | 40 分钟 |
| 模拟面试和补缺口 | 40 分钟 |

## 一、最终项目交付物

至少准备：

```text
README.md
架构图
本地运行命令
测试报告
评测报告
失败案例
安全测试结果
完整 Trace
演示输入和输出
简历项目描述
```

## 二、README 结构

建议使用下面的结构：

```markdown
# CI Failure Diagnosis Agent

## Background
## Goals
## Architecture
## Workflow
## Tools
## State and Approval
## Evaluation
## Security
## Quick Start
## Test
## Eval
## Known Limitations
```

正文使用中文，技术名保留 English。

## 三、最终评测报告

报告至少包含：

| 内容 | 说明 |
|---|---|
| Dataset Version | 使用哪一版数据 |
| Model | 使用什么模型或 Mock |
| Prompt Version | 使用哪个 Prompt |
| Tool Version | 工具是否有版本 |
| Sample Count | 样本总数 |
| Classification Accuracy | 分类准确率 |
| Tool Selection Accuracy | 工具选择准确率 |
| Evidence Accuracy | 证据引用准确率 |
| Safety Block Rate | 安全拦截率 |
| P50 / P95 Latency | 延迟 |
| Failure Cases | 失败案例 |

不要编造提升百分比。如果只是本地实验，就如实写“本地 Mock 评测结果”。

## 四、至少整理三个失败案例

每个失败案例包含：

1. 输入。
2. 预期。
3. 实际输出。
4. Trace 摘要。
5. 根因。
6. 修复。
7. 新增回归测试。
8. 修复后的结果。

推荐案例：

- 模型选择了错误 Tool。
- 检索证据不足但输出了高置信度结论。
- Prompt Injection 试图绕过审批。

## 五、90 秒项目介绍

按下面顺序表达：

### 1. 场景

我做的是一个 CI 故障诊断 Agent，帮助测试开发工程师分析流水线失败。

### 2. 输入和动作

输入包括失败日志、Stack Trace、Git Diff 和环境信息。Agent 通过日志、代码和变更查询 Tool 收集证据。

### 3. 输出

输出结构化故障分类、根因摘要、证据引用和缺陷草稿。

### 4. 工程控制

模型负责不确定的分类和 Tool 选择；状态、权限、重试、审批和写入动作由程序控制。

### 5. 质量保障

使用确定性自动化测试覆盖 Tool、Workflow 和策略逻辑，再用 Golden Dataset 评估任务完成度、分类准确率、证据质量和安全行为。

### 6. 可观测性

通过 Trace 记录模型调用、Tool 调用、状态变化、耗时和错误，支持失败回放。

## 六、面试高频问题

### 1. Agent 和 Workflow 有什么区别？

回答：

- Workflow 的步骤通常由程序定义。
- Agent 在约束下由模型选择下一步动作。
- 项目中让模型处理不确定决策，让程序处理状态、权限和副作用。

### 2. 如何测试 Agent？

回答：

- Tool Contract 和 Schema 用确定性测试。
- Workflow、State、Approval 和 Policy 用 Mock 测试。
- 任务完成度、根因判断和证据质量用 Golden Dataset。
- 通过 Trace 定位失败步骤。

### 3. 为什么不能只看最终文本？

回答：

- 最终文本可能看起来合理，但 Tool 选择错误。
- 可能引用不存在的证据。
- 可能越权调用工具。
- 可能绕过审批。

### 4. 如何处理 Tool 失败？

回答：

- 区分可重试和不可重试错误。
- 设置超时和有限重试。
- 写入动作保证幂等并需要审批。
- 最终输出说明缺失证据，不伪造结论。

### 5. MCP 解决什么问题？

回答：

- MCP 标准化了工具、资源和提示模板的接入。
- 它不替代认证、授权、审计、沙箱和评测。

## 七、最终检查表

### 项目

- [ ] 能从零启动。
- [ ] 有 Mock 模式。
- [ ] 有正常、失败和安全三种演示。
- [ ] 有完整 Trace。
- [ ] 有测试报告。
- [ ] 有评测报告。

### 技术

- [ ] 能解释 Agent、Workflow、Tool Calling。
- [ ] 能解释 RAG 和证据引用。
- [ ] 能解释 MCP 的作用和边界。
- [ ] 能解释 State、Checkpoint、Approval。
- [ ] 能解释 LLM-as-Judge 的局限。
- [ ] 能解释 Prompt Injection 和 Tool Authorization。

### 表达

- [ ] 90 秒介绍不卡顿。
- [ ] 3 分钟架构说明清楚。
- [ ] 能完整讲一个失败案例。
- [ ] 能说明项目限制。
- [ ] 没有编造线上数据或收益。

## 八、完成标准

- [ ] 所有 P0 交付物完成。
- [ ] 项目可以独立运行。
- [ ] 文档可以被其他人理解。
- [ ] 至少完成一次模拟面试。
- [ ] 已整理 2～3 条简历描述。

## 九、当天记录

```markdown
## 第 14 天记录

### 我现在能完整讲清楚的内容

-

### 还不能回答的面试问题

-

### 项目最大的限制

-

### 下一阶段要补的能力

-
```

## 参考资料

- [面试准备与项目表达](../04-interview-prep.md)
- [项目规格](../02-project-spec.md)
- [Agent 评测与测试方案](../03-agent-evaluation-and-testing.md)

