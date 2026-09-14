# 第 9 天：确定性自动化测试

## 今日目标

今天建立传统测试可以覆盖的质量底座：

> 不调用真实模型，也能验证 Tool、Schema、Workflow、权限、审批和重试逻辑。

这是你从测试开发转 Agent 工程时最应该展示的能力。

## 预计用时

| 环节 | 时间 |
|---|---:|
| 阅读测试分层 | 25 分钟 |
| 设计 Mock 边界 | 30 分钟 |
| 编写自动化测试 | 90 分钟 |
| 执行、分类失败和复盘 | 30 分钟 |

## 一、需要理解的概念

### 1. Deterministic Test

确定性测试的输入和依赖可控，同样的代码运行结果应该一致。

适合确定性测试的内容：

- Tool 参数校验。
- Tool 返回结构。
- 状态转移。
- 权限策略。
- 审批流程。
- 重试次数。
- 幂等行为。
- 脱敏逻辑。

### 2. Mock Model 和 Mock Tool

真实模型适合做行为评测，不适合验证每条状态转移。

今天要用 Mock 替换：

- 模型响应。
- 工具响应。
- 时间。
- 外部服务。

### 3. 测试层次

```text
Unit Test
  -> Tool Contract Test
  -> Workflow / Policy Test
  -> Adapter Test
  -> End-to-End Test
  -> Agent Evaluation
```

今天只做前 4 层，端到端演示放到第 13 天。

## 二、阅读顺序

1. [pytest Documentation](https://docs.pytest.org/en/stable/)
2. [OpenAI Agents SDK Testing](https://openai.github.io/openai-agents-python/testing/)
3. 回看 [Agent 评测与测试方案：测试分层](../03-agent-evaluation-and-testing.md#2-测试分层)

重点理解：

- 如何替换依赖。
- 如何断言工具调用轨迹。
- 如何测试失败和恢复。

## 三、测试目录

建议整理为：

```text
tests/
├── test_models.py
├── test_tools.py
├── test_workflow.py
├── test_policy.py
├── test_retry.py
└── test_security.py
```

## 四、动手任务

### 任务 1：模型和 Schema 测试

覆盖：

- 合法输出可以解析。
- 非法枚举被拒绝。
- 字段缺失被拒绝。
- 证据 ID 不存在被拒绝。
- 置信度越界被拒绝。

### 任务 2：Tool Contract 测试

每个 Tool 覆盖：

- 正常参数。
- 缺少参数。
- 超长参数。
- 越权项目。
- 越界路径。
- 空结果。
- 工具内部错误。

### 任务 3：Workflow 测试

覆盖：

```text
正常：CREATED -> ... -> COMPLETED
审批：DRAFTING_BUG -> WAITING_APPROVAL
拒绝：WAITING_APPROVAL -> REJECTED
失败：任意状态 -> FAILED
恢复：WAITING_APPROVAL -> APPROVED -> COMPLETED
```

### 任务 4：Policy 测试

实现并测试：

```python
is_tool_allowed(state, tool_name)
requires_approval(tool_name, state)
sanitize_output(text)
```

至少保证：

- 写入 Tool 没有审批不能执行。
- 未授权项目不能查询。
- 输出中的 Token 和密码会脱敏。

### 任务 5：重试测试

使用 Fake Tool 模拟：

```text
第一次超时，第二次成功
连续三次超时
参数错误
权限错误
```

断言：

- 重试次数。
- 最终状态。
- Trace 事件数量。
- 是否错误转人工。

## 五、建议的测试数量

最低目标：

| 类型 | 数量 |
|---|---:|
| Model / Schema | 4 |
| Tool Contract | 6 |
| Workflow | 5 |
| Policy | 4 |
| Retry / Error | 4 |
| Security 基础 | 3 |

总计至少 26 条更理想；时间紧时先保证 15 条高价值测试。

## 六、完成标准

- [ ] `pytest` 可以执行全部确定性测试。
- [ ] 测试不依赖真实模型。
- [ ] 能断言 Tool 调用名称和参数。
- [ ] 能断言状态转移。
- [ ] 能断言审批前禁止写入。
- [ ] 能断言重试次数和最终状态。
- [ ] 测试失败能定位到具体模块。

## 七、今天不要学

- 只测最终回答文本。
- 把所有判断交给 LLM-as-Judge。
- 为了覆盖率制造没有业务意义的测试。

## 八、当天记录

```markdown
## 第 9 天记录

### 我把哪些部分替换成 Mock？

-

### 最有价值的 3 条测试

1.
2.
3.

### 一个被测试发现的问题

-
```

## 参考资料

- [pytest Documentation](https://docs.pytest.org/en/stable/)
- [OpenAI Agents SDK Testing](https://openai.github.io/openai-agents-python/testing/)
- [Agent 评测与测试方案](../03-agent-evaluation-and-testing.md)

