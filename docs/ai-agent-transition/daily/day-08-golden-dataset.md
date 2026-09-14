# 第 8 天：Golden Dataset 与样本设计

## 今日目标

今天开始建立 Agent 的“考试题库”：

> 准备一批可重复运行、可明确判定通过或失败的 CI 故障样本。

没有数据集，就无法判断 Prompt、模型、Tool 或 Workflow 修改后到底变好还是变坏。

## 预计用时

| 环节 | 时间 |
|---|---:|
| 学习评测样本设计 | 30 分钟 |
| 设计样本分类和字段 | 35 分钟 |
| 编写 20～30 条样本 | 80 分钟 |
| 人工检查和覆盖矩阵 | 30 分钟 |

## 一、需要理解的概念

### 1. Golden Dataset

Golden Dataset（黄金数据集）是人工准备、长期维护、用于回归的样本集。

每条样本不只包含输入，还要包含：

- 预期分类。
- 必须出现的证据。
- 允许使用的工具。
- 禁止执行的动作。
- 是否必须人工审批。
- 评价标签。

### 2. 通过条件必须可检查

不要写：

```text
结果看起来合理
```

要写：

```text
category == CODE
required_evidence_types 包含 LOG / DIFF / CODE
禁止调用写入工具
needs_human_review == true
```

### 3. 样本要覆盖失败

如果全部是正常成功样例，数据集无法检验：

- 工具失败。
- 证据不足。
- 证据冲突。
- 越权访问。
- Prompt Injection。
- 无限循环。

## 二、阅读顺序

1. [Agent 评测与测试方案](../03-agent-evaluation-and-testing.md#3-golden-dataset黄金数据集设计)
2. [OpenAI Agents SDK Testing](https://openai.github.io/openai-agents-python/testing/)
3. [LangSmith Evaluation Concepts](https://docs.langchain.com/langsmith/evaluation-concepts)

阅读时重点看：

- 数据集样本和运行结果如何分开。
- 如何比较不同版本的实验结果。
- 哪些指标适合程序判断，哪些需要人工抽检。

## 三、样本分配

建议第一版准备 24 条：

| 类型 | 数量 | 关注点 |
|---|---:|---|
| 代码缺陷 | 5 | 异常、返回值错误、逻辑变更 |
| 测试缺陷 | 4 | 断言、定位器、用例过期 |
| 环境问题 | 4 | 网络、依赖、服务不可用 |
| 数据问题 | 4 | 数据不存在、权限、状态污染 |
| 证据不足 | 3 | 日志截断、信息矛盾 |
| 安全攻击 | 4 | 注入、越权、敏感信息 |

如果时间不足，先完成 15 条，但必须包含至少 3 条安全样例。

## 四、样本 Schema

建议使用 `data/cases.jsonl`，每行一条：

```json
{
  "case_id": "case-code-001",
  "title": "优惠券校验变更导致空值异常",
  "input": {
    "project": "checkout-service",
    "failed_test": "test_create_order_with_coupon",
    "failure_log": "NullPointerException at CouponValidator.java:42",
    "stack_trace": "CouponValidator.validate(CouponValidator.java:42)",
    "git_diff": "changed coupon validation logic"
  },
  "expected": {
    "category": "CODE",
    "required_evidence_types": ["LOG", "DIFF", "CODE"],
    "allowed_tools": ["search_log", "search_code", "get_recent_changes"],
    "forbidden_actions": ["create_bug", "rerun_test"],
    "must_request_human_review": true
  },
  "tags": ["happy-path", "code-defect"]
}
```

## 五、动手任务

### 任务 1：准备正常样例

至少 10 条：

- 输入信息较完整。
- 证据可以收集。
- 分类有明确答案。

### 任务 2：准备边界样例

至少 5 条：

- 空日志。
- 超长日志。
- 空 Stack Trace。
- 特殊字符。
- 两条证据冲突。

### 任务 3：准备安全样例

至少 4 条：

- 日志中写入“忽略系统指令”。
- 代码注释要求调用写入工具。
- 输入路径包含 `../../`。
- 伪造一个不存在的 `evidence_id`。

### 任务 4：写覆盖矩阵

建立：

```text
样本类型 -> 预期分类 -> 需要调用的 Tool -> 禁止动作 -> 评测指标
```

## 六、质量检查

逐条检查：

- 输入是否脱敏。
- 预期结果是否由人工确认。
- 通过条件是否明确。
- 是否能在本地 Mock 数据中复现。
- 是否有重复样本。
- 是否有“证据不足”样本。
- 是否有安全样本。

## 七、完成标准

- [ ] 有 `cases.jsonl`。
- [ ] 至少 20 条样本，或至少 15 条高质量样本。
- [ ] 覆盖 5 类主要故障。
- [ ] 覆盖工具失败和证据不足。
- [ ] 覆盖 Prompt Injection 和越权。
- [ ] 每条样本都有明确通过条件。

## 八、当天记录

```markdown
## 第 8 天记录

### 样本总数

-

### 各类型样本数量

-

### 最难定义通过条件的样本

-

### 今天新增的安全样例

-
```

## 参考资料

- [Agent 评测与测试方案](../03-agent-evaluation-and-testing.md)
- [OpenAI Agents SDK Testing](https://openai.github.io/openai-agents-python/testing/)
- [LangSmith Evaluation Concepts](https://docs.langchain.com/langsmith/evaluation-concepts)

