# 第 5 天：Workflow、State 与 Checkpoint

## 今日目标

今天要把前 4 天的“模型 + 工具调用”组织成一个可以暂停、恢复和审计的运行流程：

> 明确哪些决定交给模型，哪些状态和副作用由程序控制。

## 预计用时

| 环节 | 时间 |
|---|---:|
| 阅读 Workflow、State 和 Harness | 40 分钟 |
| 设计状态和状态转移 | 40 分钟 |
| 编码和持久化 | 70 分钟 |
| 模拟暂停、重启和恢复 | 30 分钟 |

## 一、需要理解的概念

### 1. Agent Loop 和 Workflow

Agent Loop 通常是：

```text
模型判断 -> 调工具 -> 观察结果 -> 再判断
```

Workflow 则明确规定：

```text
当前状态 -> 允许的动作 -> 下一状态
```

生产系统通常二者结合：

- 模型负责不确定的分类、工具选择和摘要。
- 程序负责状态、权限、重试、审批和副作用。

### 2. State 是运行事实

State（状态）应该能够回答：

- 当前运行到哪一步？
- 已经调用过哪些工具？
- 收集到了哪些证据？
- 是否等待人工审批？
- 出错后从哪里恢复？

不要从最终消息文本推断当前状态。

### 3. Checkpoint 和 Approval

Checkpoint（检查点）是可以恢复的运行保存点。

Approval（审批）不只是前端弹窗，而是一个正式状态：

```text
WAITING_APPROVAL
  -> APPROVED
  -> REJECTED
```

用户刷新页面后，审批状态仍然应该存在。

## 二、阅读顺序

1. [Agent Harness 本地笔记](../../agent-engineering-react-to-agent-harness-summary.md)：重点读 State、View、Control、Approval 和 Artifact。
2. [OpenAI Agents SDK Tracing](https://openai.github.io/openai-agents-python/tracing/)：理解主线实现如何记录一次 Run。
3. [LangGraph Overview](https://docs.langchain.com/oss/python/langgraph/overview)：作为替代实现，理解 State Graph、持久化和人工介入。
4. 回看 [项目规格：Workflow](../02-project-spec.md#5-workflow工作流)。
5. 回看 [项目规格：State Schema](../02-project-spec.md#8-state-schema状态结构)。

阅读后写下：

- 哪些字段属于 State？
- 哪些字段只是 UI View？
- 哪些操作应该通过 Control 命令进入 Runtime？

### 框架实践选择

默认使用 `OpenAI Agents SDK` 完成今天的状态和流程设计；如果希望补充替代框架，只重写下面这段最小流程：

```text
CLASSIFYING
  -> COLLECTING_EVIDENCE
  -> DIAGNOSING
  -> DRAFTING_BUG
  -> WAITING_APPROVAL
```

用 `LangGraph` 表达时，重点观察 State、节点、边、暂停和恢复；不要为了学习 API 而同时维护两套完整项目。输入、工具返回值、输出 Schema 和测试样例保持不变，才能比较框架差异。

## 三、设计 Workflow

建议先使用下面的状态：

```text
CREATED
CLASSIFYING
COLLECTING_EVIDENCE
DIAGNOSING
DRAFTING_BUG
WAITING_APPROVAL
APPROVED
COMPLETED
FAILED
STOPPED
REJECTED
```

建议流程：

```text
CREATED
  -> CLASSIFYING
  -> COLLECTING_EVIDENCE
  -> DIAGNOSING
  -> DRAFTING_BUG
  -> WAITING_APPROVAL
  -> APPROVED
  -> COMPLETED
```

错误分支：

```text
任意状态 -> FAILED
FAILED -> RETRYABLE 或 TERMINAL
WAITING_APPROVAL -> REJECTED
任意可暂停状态 -> STOPPED
```

## 四、动手任务

### 任务 1：定义 State Schema

至少包含：

```json
{
  "run_id": "run-001",
  "status": "COLLECTING_EVIDENCE",
  "current_stage": "COLLECTING_EVIDENCE",
  "input_ref": "inputs/run-001.json",
  "classification": null,
  "tool_calls": [],
  "evidence_refs": [],
  "diagnosis_ref": null,
  "artifact_refs": [],
  "pending_approval": null,
  "checkpoint": null,
  "error": null
}
```

### 任务 2：实现状态存储

第一版可以使用：

- JSON 文件。
- SQLite。

必须提供：

```python
create_run(...)
get_run(run_id)
save_state(state)
update_status(run_id, status)
```

### 任务 3：实现状态转移校验

例如：

```text
CREATED -> CLASSIFYING
CLASSIFYING -> COLLECTING_EVIDENCE
COLLECTING_EVIDENCE -> DIAGNOSING
DIAGNOSING -> DRAFTING_BUG
DRAFTING_BUG -> WAITING_APPROVAL
WAITING_APPROVAL -> APPROVED / REJECTED
APPROVED -> COMPLETED
```

禁止：

```text
CREATED -> COMPLETED
WAITING_APPROVAL -> COMPLETED
FAILED -> COMPLETED
```

### 任务 4：模拟审批恢复

实现：

```python
pause_for_approval(run_id, action, tool_name, reason)
approve(run_id, approval_token)
reject(run_id, reason)
resume(run_id)
```

测试流程：

1. 运行到 `WAITING_APPROVAL`。
2. 退出进程。
3. 重新启动。
4. 读取同一个 `run_id`。
5. 审批后继续运行。

## 五、测试任务

- 非法状态转移被拒绝。
- 审批前不能调用写入工具。
- 拒绝审批后不能进入 `APPROVED`。
- 进程重启后仍能读到 `pending_approval`。
- 已完成的 Tool Call 不会因恢复重复执行。
- Stop 后不会产生新的工具调用。
- 每次状态变更都有时间和原因。

## 六、完成标准

- [ ] 有状态枚举和状态转移表。
- [ ] 有可持久化的 State Schema。
- [ ] 能在审批点暂停。
- [ ] 能在进程重启后恢复。
- [ ] 审批前禁止高风险动作。
- [ ] 能解释 State、View、Control 的区别。

## 七、今天不要学

- 复杂 Multi-Agent 编排。
- 长时间后台任务平台。
- 分布式事件总线。
- 前端实时状态同步。

先保证本地单进程状态闭环正确。

## 八、当天记录

```markdown
## 第 5 天记录

### 哪些字段必须进入 State？

-

### 哪些字段只是 View？

-

### 我的审批状态如何恢复？

-

### 今天发现的状态转移漏洞

-
```

## 参考资料

- [Agent Harness 本地笔记](../../agent-engineering-react-to-agent-harness-summary.md)
- [OpenAI Agents SDK Tracing](https://openai.github.io/openai-agents-python/tracing/)
- [LangGraph Overview](https://docs.langchain.com/oss/python/langgraph/overview)
- [项目规格：Workflow 和 State](../02-project-spec.md)
