# 第 1 天：最小 Agent 闭环与模型调用

## 今日目标

今天只解决一个问题：

> 能不能把一条 CI 失败信息交给模型，并得到一个程序可以继续使用的故障分类结果？

今天不接工具、不做 RAG、不做 Multi-Agent。先理解一次最小 Agent Run（智能体运行）是如何发生的。

## 预计用时

| 环节 | 时间 |
|---|---:|
| 阅读概念 | 30 分钟 |
| 初始化项目和模型调用 | 60 分钟 |
| 编写样例和测试 | 40 分钟 |
| 记录问题和复盘 | 20 分钟 |

## 一、需要理解的概念

### 1. 普通函数和 Agent 的区别

普通函数通常是：

```text
输入 -> 固定代码逻辑 -> 输出
```

Agent 通常是：

```text
任务和上下文 -> 模型判断下一步 -> 返回结果或请求动作
```

今天先只实现“模型判断”，把工具调用留到第 3 天。

### 2. 一次最小运行包含什么

至少需要知道这些对象：

- `Agent`：模型、指令和输出要求的组合。
- `Runner`：驱动一次 Agent Run 的执行器。
- `Input`：用户任务或业务输入。
- `Output`：模型最终生成的结果。
- `Run ID`：用于关联日志、错误和后续 Trace 的唯一标识。

### 3. Prompt 的基本分工

- System Prompt：定义身份、任务边界和不能做什么。
- User Prompt：提供当前任务和现场数据。
- Context：系统检索或程序计算后补充的上下文。

不要把权限规则全部写在 User Prompt 中。后面会由程序和 Tool 层再次校验。

### 4. 第一版故障分类

固定为以下 5 类：

```text
CODE
TEST
ENVIRONMENT
DATA
UNKNOWN
```

含义：

- `CODE`：被测代码或服务实现问题。
- `TEST`：测试脚本、断言或定位逻辑问题。
- `ENVIRONMENT`：网络、依赖、容器或服务环境问题。
- `DATA`：测试数据、状态污染或权限数据问题。
- `UNKNOWN`：证据不足，暂时无法判断。

## 二、阅读顺序

只阅读下面内容，不要扩展到 Agent Framework 对比：

1. [OpenAI Agents SDK Agents](https://openai.github.io/openai-agents-python/agents/)：理解 Agent 的组成。
2. [OpenAI Agents SDK 官方文档](https://openai.github.io/openai-agents-python/)：跑一遍最小示例。
3. [Pydantic Models](https://docs.pydantic.dev/latest/concepts/models/)：了解如何定义结果对象。

阅读时重点回答：

- Agent 和 Runner 分别负责什么？
- 模型输出如何传给程序？
- 为什么要保留 `run_id`？

## 三、动手任务

在 `projects/ci-failure-agent/` 下完成以下文件：

```text
app/
├── models.py
├── classifier.py
└── main.py
tests/
└── test_classifier.py
data/
└── samples.jsonl
```

### 任务 1：定义输入模型

定义 `FailureInput`，至少包含：

```python
project
pipeline_id
failed_test
failure_log
stack_trace
environment
```

### 任务 2：定义分类结果

定义 `FailureClassification`，至少包含：

```python
category
summary
confidence
run_id
```

今天可以先让 `evidence_ids` 为空列表，明天再补证据约束。

### 任务 3：实现分类函数

实现：

```python
def classify_failure(
    failure: FailureInput,
) -> FailureClassification:
    ...
```

建议先实现两种模式：

1. `mock` 模式：不调用真实模型，用固定规则返回结果。
2. `llm` 模式：通过模型 API 返回结果。

这样没有 API Key 时也可以完成测试。

### 任务 4：准备 5 条样例

至少包括：

1. 空指针异常，分类为 `CODE`。
2. 元素定位失败，分类为 `TEST`。
3. 依赖服务连接超时，分类为 `ENVIRONMENT`。
4. 测试用户不存在，分类为 `DATA`。
5. 只有一句模糊错误信息，分类为 `UNKNOWN`。

## 四、测试任务

至少写 5 条测试：

- 输入字段缺失时失败。
- 分类值不在枚举范围时失败。
- 置信度小于 0 或大于 1 时失败。
- `mock` 模式可以稳定返回结果。
- 同一条输入连续运行 3 次，结构都合法。

今天不要追求分类准确率。重点是先保证“输入、模型结果、程序校验”这条链路跑通。

## 五、完成标准

- [ ] 可以本地运行一次分类任务。
- [ ] 有 `FailureInput` 和 `FailureClassification`。
- [ ] 有 5 条样例数据。
- [ ] 有至少 5 条测试。
- [ ] 非法结果不会直接进入后续流程。
- [ ] 日志中包含 `run_id`。
- [ ] 能解释 Agent、Runner、Input、Output 的关系。

## 六、今天不要学

- Multi-Agent。
- 复杂 RAG。
- 向量数据库。
- MCP。
- Agent Memory。
- Prompt 自动优化。

## 七、当天记录

```markdown
## 第 1 天记录

### 我今天真正理解的 3 个概念

1.
2.
3.

### 模型输出出现过的异常

-

### 我写的测试

-

### 明天需要解决的问题

-
```

## 参考资料

- [OpenAI Agents SDK Agents](https://openai.github.io/openai-agents-python/agents/)
- [OpenAI Agents SDK 官方文档](https://openai.github.io/openai-agents-python/)
- [Pydantic Models](https://docs.pydantic.dev/latest/concepts/models/)

