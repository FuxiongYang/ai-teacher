# 第 2 天：Prompt 与结构化输出

## 今日目标

今天要把昨天“模型给出一个分类”的结果，升级成：

> 模型输出可以被 Schema 校验，业务程序可以拒绝不合法、不完整或没有证据的结果。

重点不是写更长的 Prompt，而是建立“模型输出 + 程序校验 + 失败处理”的边界。

## 预计用时

| 环节 | 时间 |
|---|---:|
| 阅读 Prompt 和 Structured Outputs | 35 分钟 |
| 设计结果 Schema | 35 分钟 |
| 编码和异常样例 | 60 分钟 |
| 测试和复盘 | 30 分钟 |

## 一、需要理解的概念

### 1. Prompt 负责什么

Prompt 可以说明：

- 任务目标。
- 分类标准。
- 输入字段含义。
- 输出字段含义。
- 不确定时如何处理。
- 哪些行为禁止执行。

Prompt 不能独立保证：

- 权限安全。
- 工具参数合法。
- 证据真实存在。
- 高风险动作经过审批。
- 输出永远符合业务规则。

### 2. Structured Outputs 和 JSON Mode 的区别

你需要理解：

- JSON Mode 主要保证结果是 JSON。
- Structured Outputs 进一步要求结果遵循指定 Schema。
- 即使有 Schema，业务语义仍然需要程序校验。

例如，`evidence_ids` 的格式合法，不代表这些 ID 真的存在。

### 3. 模型判断和程序判断分工

模型可以负责：

- 从自然语言和日志中提取候选分类。
- 生成摘要。
- 提出下一步建议。

程序必须负责：

- 枚举是否合法。
- 置信度范围是否合法。
- 证据 ID 是否存在。
- 是否允许调用某个动作。
- 是否必须人工审批。

## 二、阅读顺序

1. [OpenAI Structured Outputs](https://developers.openai.com/api/docs/guides/structured-outputs)
2. [Pydantic Validation](https://docs.pydantic.dev/latest/concepts/validators/)
3. 回看 [项目规格：诊断结果](../02-project-spec.md#62-诊断结果)

阅读时记录：

- 哪些字段适合枚举？
- 哪些字段可以为空？
- 哪些字段不能相信模型，需要程序二次判断？

## 三、今天的输出模型

把 `FailureClassification` 升级为 `DiagnosisResult`：

```json
{
  "category": "CODE",
  "summary": "优惠券校验逻辑对空值处理不完整",
  "confidence": 0.86,
  "evidence_ids": ["log-001", "diff-002"],
  "next_action": "CREATE_BUG_DRAFT",
  "needs_human_review": true,
  "uncertainties": []
}
```

字段要求：

| 字段 | 约束 |
|---|---|
| `category` | 只能是 5 个故障类别 |
| `summary` | 非空，长度有上限 |
| `confidence` | 0 到 1 之间 |
| `evidence_ids` | 字符串列表 |
| `next_action` | 只能是允许的动作 |
| `needs_human_review` | 布尔值 |
| `uncertainties` | 不确定点列表 |

## 四、动手任务

### 任务 1：定义完整 Schema

在 `app/models.py` 中增加：

```text
DiagnosisResult
EvidenceRef
RecommendedAction
```

建议动作：

```text
COLLECT_MORE_EVIDENCE
CREATE_BUG_DRAFT
WAIT_FOR_HUMAN
STOP
```

### 任务 2：编写系统 Prompt

Prompt 至少包含：

1. 角色：CI 故障诊断助手。
2. 目标：基于输入和证据判断故障类型。
3. 分类定义。
4. 证据规则：不能引用不存在的证据。
5. 不确定规则：证据不足时使用 `UNKNOWN`。
6. 动作规则：不能直接创建真实缺陷。
7. 输出规则：只输出指定 Schema。

### 任务 3：构造异常输出

手工准备以下模型输出：

```json
{"category": "BUG", "confidence": 1.5}
```

```json
{"category": "CODE", "confidence": 0.8, "evidence_ids": ["not-exist"]}
```

```json
{"category": "CODE", "confidence": 0.8, "evidence_ids": []}
```

验证程序是否能分别报出：

- 非法枚举。
- 置信度越界。
- 证据不存在。
- 证据缺失。

### 任务 4：实现失败处理

当输出解析失败时，第一版可以选择：

- 有限次数重试。
- 返回结构化错误。
- 进入 `WAIT_FOR_HUMAN`。

不要无限重试，也不要用字符串截取“修好”所有 JSON。

## 五、测试任务

新增测试：

- 合法 `DiagnosisResult` 可以解析。
- 非法 `category` 被拒绝。
- `confidence=1.01` 被拒绝。
- `summary` 为空被拒绝。
- 引用不存在的 `evidence_id` 被拒绝。
- `CREATE_BUG_DRAFT` 时 `needs_human_review` 必须为 `true`。
- 输出声称“已创建缺陷”时被程序拦截。

## 六、完成标准

- [ ] 有完整 `DiagnosisResult`。
- [ ] Prompt 中有明确的任务边界和不确定规则。
- [ ] 有至少 5 个异常输出样例。
- [ ] 有输出 Schema 校验。
- [ ] 有失败后的有限重试或转人工策略。
- [ ] 程序不会仅凭模型输出决定危险动作。

## 七、今天不要学

- Prompt 自动搜索。
- Fine-tuning。
- RLHF。
- 复杂 Agent Memory。
- 多模型路由。

## 八、当天记录

```markdown
## 第 2 天记录

### 哪些规则写进了 Prompt？

-

### 哪些规则由程序负责？

-

### 哪一种非法输出最容易漏掉？

-

### 新增测试数量

-
```

## 参考资料

- [OpenAI Structured Outputs](https://developers.openai.com/api/docs/guides/structured-outputs)
- [Pydantic Validation](https://docs.pydantic.dev/latest/concepts/validators/)
- [项目规格：输入和输出 Schema](../02-project-spec.md#6-输入和输出-schema结构约束)

