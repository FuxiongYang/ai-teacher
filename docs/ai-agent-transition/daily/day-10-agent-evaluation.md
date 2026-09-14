# 第 10 天：Agent 行为评测

## 今日目标

今天把第 8 天的数据集和第 9 天的测试真正跑起来：

> 评估 Agent 是否完成任务、选择了正确工具、引用了正确证据，并且没有执行危险动作。

今天不追求做一个复杂评测平台，先做一个可以批量运行、输出 JSON 报告的脚本。

## 预计用时

| 环节 | 时间 |
|---|---:|
| 学习评测指标 | 30 分钟 |
| 编写评测执行器 | 60 分钟 |
| 实现指标和报告 | 60 分钟 |
| 分析失败样例 | 30 分钟 |

## 一、需要理解的概念

### 1. Agent Evaluation

Agent Evaluation（智能体评测）不是只比较最终文本相似度，而是检查：

- 任务是否完成。
- 分类是否正确。
- Tool 是否选择正确。
- Tool 参数是否正确。
- 证据是否真实且支持结论。
- 是否遵守审批和权限规则。

### 2. 确定性指标和开放式指标

适合程序直接判断：

- 分类枚举。
- 工具名称。
- 工具参数。
- 是否调用危险 Tool。
- 证据 ID 是否存在。
- 是否进入审批状态。

需要人工或 LLM-as-Judge 辅助：

- 根因摘要是否清楚。
- 证据是否真正支持结论。
- 不确定性表达是否恰当。
- 缺陷草稿是否有用。

### 3. 基线

第一次评测结果就是基线，不要只记录一个总分。

至少保存：

```text
model
prompt_version
tool_version
dataset_version
metrics
failures
timestamp
```

## 二、阅读顺序

1. [Agent 评测与测试方案](../03-agent-evaluation-and-testing.md)
2. [OpenAI Agents SDK Testing](https://openai.github.io/openai-agents-python/testing/)
3. [LangSmith Evaluation Concepts](https://docs.langchain.com/langsmith/evaluation-concepts)

阅读重点：

- Dataset、Run、Experiment 的关系。
- 如何保存每条样本的输入、输出和评分。
- 为什么失败样例比平均分更重要。

## 三、第一版指标

### 1. 分类准确率

```text
正确分类样本数 / 有明确答案的样本数
```

### 2. Tool 选择准确率

检查：

- 必须调用的 Tool 是否调用。
- 禁止调用的 Tool 是否没有调用。
- 是否调用了与任务无关的 Tool。

### 3. 证据引用准确率

分成：

- 存在：ID 能查到。
- 相关：证据支持结论。
- 完整：关键事实没有遗漏。

### 4. 安全指标

- 危险动作拦截率。
- 越权访问拒绝率。
- Prompt Injection 拒绝率。
- 敏感信息泄露率。

### 5. 工程指标

- P50 / P95 延迟。
- 平均模型调用次数。
- 平均 Token。
- 工具失败率。
- 单次任务成本。

## 四、动手任务

### 任务 1：实现评测入口

创建：

```text
evals/
├── run_eval.py
├── metrics.py
└── reports/
```

运行方式建议：

```bash
python -m evals.run_eval \
  --dataset data/cases.jsonl \
  --mode mock \
  --output evals/reports/baseline.json
```

### 任务 2：执行每条样本

每条样本保存：

```json
{
  "case_id": "case-code-001",
  "status": "PASS",
  "actual_category": "CODE",
  "expected_category": "CODE",
  "tool_calls": ["search_log", "search_code"],
  "evidence_ids": ["log-001", "code-002"],
  "violations": [],
  "latency_ms": 420
}
```

### 任务 3：实现规则评分

第一版先使用规则：

- `category` 是否相等。
- 必需证据类型是否都存在。
- 禁止动作是否未出现。
- `needs_human_review` 是否符合预期。
- 引用 ID 是否真实存在。

### 任务 4：选 5 条样例做人审

人工检查：

- 根因摘要是否被证据支持。
- 是否把相关内容误认为根因。
- 证据不足时是否表达不确定。
- 缺陷草稿是否可交给开发确认。

## 五、LLM-as-Judge 的使用边界

如果使用 LLM-as-Judge：

输入应包含：

- 原始任务。
- 预期标准。
- Agent 输出。
- 引用证据。
- Tool Trace。

评分维度可以是 0～2：

```text
分类正确性
证据相关性
证据完整性
不确定性表达
输出可执行性
安全合规
```

不要让评审模型单独决定：

- 是否越权。
- 是否调用危险 Tool。
- 证据 ID 是否存在。
- 是否真的经过审批。

这些应由程序断言。

## 六、完成标准

- [ ] 有 `run_eval.py`。
- [ ] 能批量运行 Golden Dataset。
- [ ] 有第一版基线报告。
- [ ] 至少计算 4 类指标。
- [ ] 有 5 条失败样例分析。
- [ ] 能区分程序规则评分和模型辅助评分。

## 七、当天记录

```markdown
## 第 10 天记录

### 当前基线指标

| 指标 | 结果 |
|---|---:|
| 分类准确率 | |
| Tool 选择准确率 | |
| 证据引用准确率 | |
| 危险动作拦截率 | |

### 最严重的失败样例

-

### 失败属于哪一层？

- [ ] Prompt
- [ ] Model
- [ ] Tool
- [ ] Workflow
- [ ] Dataset
- [ ] Evaluation Rule
```

## 参考资料

- [Agent 评测与测试方案](../03-agent-evaluation-and-testing.md)
- [OpenAI Agents SDK Testing](https://openai.github.io/openai-agents-python/testing/)
- [LangSmith Evaluation Concepts](https://docs.langchain.com/langsmith/evaluation-concepts)

