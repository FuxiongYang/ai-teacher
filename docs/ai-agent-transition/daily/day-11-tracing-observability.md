# 第 11 天：Trace 与可观测性

## 今日目标

今天要让一次 Agent 运行“可解释、可回放、可定位”：

> 看到最终答案之外，还能知道模型调用了什么、Tool 返回了什么、状态怎么变化、失败发生在哪里。

## 预计用时

| 环节 | 时间 |
|---|---:|
| 阅读 Trace 和 Span | 30 分钟 |
| 设计事件结构 | 35 分钟 |
| 接入 Trace 记录 | 80 分钟 |
| 用失败样例做回放 | 30 分钟 |

## 一、需要理解的概念

### 1. Trace 和 Log 的区别

Log 通常描述一条事件：

```text
search_log failed
```

Trace 描述一次完整运行：

```text
Run
  -> Model Call
  -> Tool Call
  -> Tool Result
  -> State Change
  -> Model Call
  -> Final Output
```

### 2. Run、Span 和 Event

- Run：一次完整 Agent 任务。
- Span：Run 中的一段操作，例如一次模型调用或 Tool 调用。
- Event：发生的事实，例如 `approval.required`。

### 3. 可观测性的三个问题

每次失败都要能回答：

1. 发生了什么？
2. 为什么发生？
3. 下一步怎么重放或修复？

## 二、阅读顺序

1. [OpenAI Agents SDK Tracing](https://openai.github.io/openai-agents-python/tracing/)
2. [Agent Harness 本地笔记](../../agent-engineering-react-to-agent-harness-summary.md)
3. 回看 [项目规格：事件和 Trace](../02-project-spec.md#9-事件和-trace运行轨迹)

重点关注：

- Trace、Span 和自定义事件。
- 如何关联父子调用。
- 哪些数据需要脱敏。

## 三、设计 Trace Schema

至少保存：

```json
{
  "event_id": "event-001",
  "run_id": "run-001",
  "parent_id": null,
  "event_type": "tool.completed",
  "timestamp": "2026-09-14T10:00:00Z",
  "stage": "COLLECTING_EVIDENCE",
  "tool_name": "search_log",
  "tool_args": {
    "run_id": "run-001",
    "query": "NullPointerException"
  },
  "status": "OK",
  "duration_ms": 120,
  "token_usage": null,
  "error": null
}
```

模型调用还应记录：

```text
model
prompt_version
input_tokens
output_tokens
temperature
```

不要记录：

- API Key。
- 明文密码。
- 未脱敏的访问 Token。
- 不必要的个人敏感信息。

## 四、动手任务

### 任务 1：实现事件记录器

提供：

```python
record_event(...)
start_span(...)
finish_span(...)
get_trace(run_id)
```

第一版可以写入：

```text
traces/{run_id}.jsonl
```

### 任务 2：给关键节点加事件

至少记录：

```text
run.created
stage.started
model.called
tool.requested
tool.started
tool.completed
evidence.added
diagnosis.created
approval.required
approval.resolved
run.failed
run.completed
```

### 任务 3：给失败运行做回放

构造一个：

```text
search_log 超时
  -> 重试
  -> 再次失败
  -> 进入 FAILED 或 WAITING_HUMAN
```

用 Trace 说明：

- 第一次错误是什么。
- 重试是否发生。
- 为什么最终停止。
- State 最后是什么。

### 任务 4：计算工程指标

从 Trace 中计算：

- 总耗时。
- 各阶段耗时。
- Tool 调用次数。
- Tool 失败率。
- 模型调用次数。
- Token 总量。

## 五、测试任务

- 每个事件包含 `run_id`。
- 子事件包含正确的 `parent_id`。
- 失败事件包含 `error_code`。
- Trace 顺序与状态转移一致。
- 敏感字段会脱敏。
- 进程重启后 Trace 文件仍可读取。
- 同一个 Run 可以完整回放。

## 六、完成标准

- [ ] 能保存一次完整 Trace。
- [ ] 能看到模型、Tool、State 和 Error。
- [ ] 能回放一个失败 Run。
- [ ] 能计算延迟和调用次数。
- [ ] 已处理敏感信息脱敏。
- [ ] 能解释 Trace 和普通日志的区别。

## 七、当天记录

```markdown
## 第 11 天记录

### 一次完整 Run 包含哪些事件？

-

### 我回放的失败是什么？

-

### Trace 帮我定位到了哪一层？

- [ ] Model
- [ ] Prompt
- [ ] Tool
- [ ] Workflow
- [ ] State
- [ ] Policy

### 今天增加的工程指标

-
```

## 参考资料

- [OpenAI Agents SDK Tracing](https://openai.github.io/openai-agents-python/tracing/)
- [Agent Harness 本地笔记](../../agent-engineering-react-to-agent-harness-summary.md)
- [项目规格：事件和 Trace](../02-project-spec.md#9-事件和-trace运行轨迹)

