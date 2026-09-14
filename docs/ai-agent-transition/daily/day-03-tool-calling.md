# 第 3 天：Tool Calling 与工具契约

## 今日目标

今天让 Agent 真正“做事”：

> 根据失败信息选择合适的只读工具，传入合法参数，并把工具结果继续交给模型。

今天不做写入工具，不允许执行任意 Shell。

## 预计用时

| 环节 | 时间 |
|---|---:|
| 阅读 Tool Calling | 30 分钟 |
| 设计 3 个工具 | 30 分钟 |
| 编码和接入 Agent | 80 分钟 |
| 工具单测和复盘 | 30 分钟 |

## 一、需要理解的概念

### 1. Tool 的四个边界

每个工具都必须说清楚：

1. 它能做什么。
2. 它不能做什么。
3. 它需要哪些参数。
4. 它返回什么结构。

工具不是“把一个大函数暴露给模型”。工具越大，权限越难控制，失败越难定位。

### 2. 只读工具和写入工具

只读工具：

- 查询日志。
- 查询代码。
- 查询 Git Diff。

写入工具：

- 重跑测试。
- 创建缺陷。
- 修改代码。

第 3 天只实现只读工具。写入工具在第 5 天加入审批状态，第 12 天做安全测试。

### 3. Tool Contract

Tool Contract（工具契约）至少包括：

```text
name
description
input_schema
output_schema
permission_scope
timeout
retry_policy
audit_fields
```

## 二、阅读顺序

1. [OpenAI Agents SDK Tools](https://openai.github.io/openai-agents-python/tools/)
2. [OpenAI Agents SDK Agents](https://openai.github.io/openai-agents-python/agents/)
3. 回看 [项目规格：工具设计](../02-project-spec.md#7-工具设计)

重点关注：

- Function Tool 如何生成参数 Schema。
- Tool 返回值如何进入下一轮模型上下文。
- 工具错误如何传回 Agent。

## 三、今天实现的工具

### 1. `search_log`

输入：

```text
run_id
query
limit
```

返回：

```json
{
  "status": "OK",
  "evidence": [
    {
      "evidence_id": "log-001",
      "source_type": "LOG",
      "source_uri": "ci/run-001.log",
      "content": "NullPointerException at CouponValidator.java:42"
    }
  ]
}
```

### 2. `search_code`

输入：

```text
keyword
file_hint
commit
```

约束：

- 只能读配置允许的代码目录。
- 返回文件路径和行号。
- 不执行代码。

### 3. `get_recent_changes`

输入：

```text
service
since
until
```

约束：

- 只能查询当前项目。
- 限制时间范围。
- 限制返回 Diff 大小。

## 四、动手任务

### 任务 1：建立 Tool 注册表

建立类似结构：

```python
TOOLS = {
    "search_log": search_log,
    "search_code": search_code,
    "get_recent_changes": get_recent_changes,
}
```

注册表需要保存工具元数据，不要只保存函数对象。

### 任务 2：给每个工具定义 Schema

每个工具的参数都要校验：

- 必填字段。
- 最大长度。
- 枚举值。
- 数值上限。
- 项目和路径范围。

### 任务 3：把工具接入 Agent

执行一次这样的流程：

```text
用户输入失败信息
  -> Agent 选择 search_log
  -> 得到日志证据
  -> Agent 选择 search_code
  -> 得到代码证据
  -> 输出当前诊断
```

记录：

- `tool_name`
- `tool_args`
- `call_id`
- `status`
- `duration_ms`

### 任务 4：模拟模型选择错误

人为构造：

- 模型请求不存在的工具。
- 模型传入空 `run_id`。
- 模型要求访问未授权路径。
- 模型传入超大 `limit`。

验证 Tool 层是否能拒绝，而不是把错误交给模型“自我修正”。

## 五、测试任务

每个工具至少写 3 条测试：

- 合法参数返回结构正确。
- 缺少必填参数时失败。
- 越界参数时失败。

额外写：

- 未知工具名被拒绝。
- 工具输出包含 `evidence_id`。
- 工具错误包含稳定的 `error_code`。
- 工具调用有唯一 `call_id`。

## 六、完成标准

- [ ] 有 3 个只读工具。
- [ ] 每个工具都有输入和输出 Schema。
- [ ] Agent 至少完成一次两步工具调用。
- [ ] 工具调用有结构化日志。
- [ ] 未知工具、非法参数和越权路径都会被拒绝。
- [ ] 没有执行任意 Shell。

## 七、当天记录

```markdown
## 第 3 天记录

### 三个工具分别解决什么问题？

-

### 哪个参数最需要做权限校验？

-

### 模型选错工具时，程序如何处理？

-

### 新增工具测试数量

-
```

## 参考资料

- [OpenAI Agents SDK Tools](https://openai.github.io/openai-agents-python/tools/)
- [OpenAI Agents SDK Agents](https://openai.github.io/openai-agents-python/agents/)
- [项目规格：工具设计](../02-project-spec.md#7-工具设计)

