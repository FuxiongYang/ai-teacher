# 第 6 天：检索、RAG 与证据链

## 今日目标

今天要让 Agent 的诊断结论有证据支撑：

> Agent 不只是“猜原因”，而是从日志、代码和变更中检索证据，并引用真实存在的 `evidence_id`。

## 预计用时

| 环节 | 时间 |
|---|---:|
| 阅读检索和 RAG 基础 | 35 分钟 |
| 准备本地数据 | 30 分钟 |
| 实现检索和证据存储 | 80 分钟 |
| 证据引用测试 | 30 分钟 |

## 一、需要理解的概念

### 1. RAG 的最小流程

```text
用户问题
  -> 查询改写或关键词提取
  -> 检索
  -> 过滤和排序
  -> 证据放入上下文
  -> Agent 生成结论
```

今天先做关键词检索或 SQLite FTS，不要求向量数据库。

### 2. 证据和上下文不是一回事

上下文是给模型看的内容，证据是系统可追溯的事实。

每条证据至少要有：

```text
evidence_id
source_type
source_uri
content
score
version
```

### 3. 证据不足时必须承认不确定

以下情况不能强行得出高置信度结论：

- 日志为空。
- Stack Trace 被截断。
- 代码和 Diff 没有对应关系。
- 多条证据互相冲突。
- 检索只返回相似内容，无法支持结论。

## 二、阅读顺序

1. [OpenAI Retrieval](https://developers.openai.com/api/docs/guides/retrieval)：理解检索增强的基本流程。
2. [OpenAI Agents SDK Tools](https://openai.github.io/openai-agents-python/tools/)：了解如何把检索封装成 Tool。
3. 回看 [项目规格：State Schema](../02-project-spec.md#8-state-schema状态结构)。

阅读时重点关注：

- 检索结果如何保留来源。
- 召回内容如何进入模型上下文。
- 如何避免把检索结果当成系统指令。

## 三、准备本地数据

创建：

```text
data/
├── logs/
├── code/
├── diffs/
└── history/
```

至少准备 5 组故障现场：

1. 空指针异常和对应代码。
2. 测试定位器失效。
3. 依赖服务超时。
4. 测试数据不存在。
5. 日志中没有足够信息。

每组数据包含：

```text
run_id
project
commit
failed_test
log
code_snippet
git_diff
expected_category
```

## 四、动手任务

### 任务 1：定义 `Evidence`

```json
{
  "evidence_id": "log-001",
  "source_type": "LOG",
  "source_uri": "data/logs/run-001.log",
  "content": "NullPointerException at CouponValidator.java:42",
  "score": 0.92,
  "version": "abc123"
}
```

### 任务 2：实现本地检索

先实现：

```python
search_log(run_id, query, limit)
search_code(keyword, file_hint, commit)
search_diff(service, since, until)
```

检索顺序可以是：

1. 过滤项目和 `run_id`。
2. 关键词匹配。
3. 按命中次数或来源优先级排序。
4. 截断内容长度。
5. 分配 `evidence_id`。

### 任务 3：保存证据引用

不要只把文本拼接进 Prompt。把证据保存到 Evidence Store，并在 State 中保存：

```json
{
  "evidence_refs": ["log-001", "code-002", "diff-003"]
}
```

### 任务 4：约束诊断输出

诊断输出中的 `evidence_ids` 必须满足：

- ID 存在。
- ID 属于当前 `run_id`。
- ID 对应的内容支持结论。

## 五、测试任务

- 查询不存在的 `run_id` 返回空结果或稳定错误。
- 结果不会访问其他项目的数据。
- 证据 ID 全局唯一或在 Run 内唯一。
- 输出引用不存在的证据时被拒绝。
- 检索不到时使用 `UNKNOWN` 或转人工。
- 超长日志会被截断。
- 日志里的“忽略系统提示”只作为内容，不改变系统规则。

## 六、完成标准

- [ ] 有本地日志、代码和 Diff 数据。
- [ ] 有统一的 `Evidence` 结构。
- [ ] 至少有两个检索 Tool。
- [ ] Agent 结论引用真实 `evidence_id`。
- [ ] 证据不足时不会编造来源。
- [ ] 能解释关键词检索和向量检索的差异。

## 七、今天不要学

- 复杂 Embedding 模型训练。
- 自建向量数据库集群。
- Reranker 调优。
- 大规模文档切分平台。

先把证据归属和可追溯性做好。

## 八、当天记录

```markdown
## 第 6 天记录

### 我准备了哪些故障样例？

-

### 哪条证据最能支持根因？

-

### 检索不到证据时系统怎么做？

-

### 今天新增的证据引用测试

-
```

## 参考资料

- [OpenAI Retrieval](https://developers.openai.com/api/docs/guides/retrieval)
- [OpenAI Agents SDK Tools](https://openai.github.io/openai-agents-python/tools/)
- [项目规格：工具设计](../02-project-spec.md#7-工具设计)

