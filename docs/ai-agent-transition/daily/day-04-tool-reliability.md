# 第 4 天：工具可靠性、重试与幂等

## 今日目标

今天不增加新功能，而是让昨天的工具在失败时表现得像一个合格的软件组件：

> 工具超时可控、临时错误有限重试、永久错误快速失败、重复写入不会产生副作用。

## 预计用时

| 环节 | 时间 |
|---|---:|
| 阅读错误处理和异步超时 | 30 分钟 |
| 设计错误分类和策略 | 30 分钟 |
| 编码实现 | 70 分钟 |
| 故障注入测试 | 40 分钟 |

## 一、需要理解的概念

### 1. 错误分类

建议把工具错误分成：

| 错误类型 | 示例 | 是否重试 |
|---|---|---|
| `TIMEOUT` | 日志服务超时 | 是，次数有限 |
| `RATE_LIMITED` | 访问频率过高 | 是，退避 |
| `TEMPORARY` | 临时网络失败 | 是，次数有限 |
| `INVALID_ARGUMENT` | 参数缺失或非法 | 否 |
| `NOT_FOUND` | 没有匹配数据 | 通常否 |
| `FORBIDDEN` | 没有权限 | 否 |
| `INTERNAL` | 工具内部错误 | 视情况 |

### 2. 重试不是万能修复

不可重试的错误如果反复重试，会带来：

- 延迟增加。
- Token 增加。
- 日志噪声。
- 写入动作重复。
- 真实问题被掩盖。

### 3. 幂等

幂等意味着同一个动作执行一次或多次，最终业务结果一致。

对未来的 `create_bug_draft`，至少准备：

```text
idempotency_key = run_id + action + content_hash
```

今天可以先在工具层定义这个字段，不需要真正提交缺陷。

## 二、阅读顺序

1. [Python asyncio](https://docs.python.org/3/library/asyncio.html)：只看 timeout、task cancellation 相关内容。
2. [OpenAI Agents SDK Tools](https://openai.github.io/openai-agents-python/tools/)：看工具错误和超时。
3. 回看 [项目规格：Workflow](../02-project-spec.md#5-workflow工作流)。

## 三、动手任务

### 任务 1：统一工具错误

定义：

```python
class ToolError:
    error_code: str
    message: str
    retryable: bool
    retry_after_ms: int | None
```

不要只返回一段字符串错误。

### 任务 2：实现有限重试

建议第一版策略：

```text
最多重试 2 次
指数退避
只重试 TIMEOUT / RATE_LIMITED / TEMPORARY
INVALID_ARGUMENT / FORBIDDEN 不重试
```

记录每次重试：

- 当前次数。
- 原始错误。
- 等待时间。
- 最终状态。

### 任务 3：加入超时

每个工具都要有最大执行时间，例如：

```text
search_log: 3 秒
search_code: 3 秒
get_recent_changes: 5 秒
```

超时后要进入明确的 `TIMEOUT` 状态，不能一直等待。

### 任务 4：故障注入

实现一个测试用 Fake Tool，可以按配置返回：

```text
第一次超时，第二次成功
连续三次超时
参数错误
权限错误
空结果
```

## 四、测试任务

至少覆盖：

- 第一次超时、第二次成功。
- 连续超时后达到重试上限。
- 非法参数不重试。
- 权限错误不重试。
- 重试过程不会超过最大次数。
- 每次调用有不同 `call_id`。
- 同一个幂等键不会重复写入。
- 工具失败后最终结果会说明证据缺失。

## 五、完成标准

- [ ] 有稳定的错误码。
- [ ] 有超时机制。
- [ ] 有有限重试和退避。
- [ ] 已区分可重试和不可重试错误。
- [ ] 写入动作已经预留幂等键。
- [ ] 有故障注入测试。
- [ ] 不会因为工具失败进入无限循环。

## 六、今天不要学

- 分布式任务队列。
- 复杂服务治理。
- Kubernetes。
- 复杂熔断组件。

先把 Agent Tool 层的失败行为讲清楚。

## 七、当天记录

```markdown
## 第 4 天记录

### 我定义了哪些错误类型？

-

### 哪些错误可以重试？为什么？

-

### 哪些动作必须幂等？

-

### 故障注入结果

-
```

## 参考资料

- [Python asyncio](https://docs.python.org/3/library/asyncio.html)
- [OpenAI Agents SDK Tools](https://openai.github.io/openai-agents-python/tools/)
- [项目规格：工具设计](../02-project-spec.md#7-工具设计)

