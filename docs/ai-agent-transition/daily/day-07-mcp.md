# 第 7 天：MCP 最小实践

## 今日目标

今天只做一个 MCP（Model Context Protocol，模型上下文协议）Tool：

> 把本地的 `search_log` 或 `search_code` 暴露为标准化的外部能力，并理解 MCP 的边界。

不需要今天掌握所有协议细节，也不需要做复杂 MCP Server 集群。

## 预计用时

| 环节 | 时间 |
|---|---:|
| 阅读 MCP 基础 | 40 分钟 |
| 选择并改造一个 Tool | 30 分钟 |
| 编写 MCP Server / Client | 70 分钟 |
| 调试、权限测试和复盘 | 30 分钟 |

## 一、需要理解的概念

### 1. MCP 解决什么问题

MCP 主要解决：

- 工具如何被发现。
- 工具参数如何描述。
- 外部资源如何接入。
- Agent 应用如何与工具服务通信。

MCP 不会自动解决：

- 业务身份认证。
- 项目权限。
- 数据脱敏。
- 审计。
- 沙箱。
- Agent 评测。

### 2. 三类能力

- Tools：可以被调用的动作。
- Resources：可以被读取的外部上下文。
- Prompts：可以复用的提示模板。

今天只实现一个 Tool。

### 3. 普通函数和 MCP Tool

普通函数：

```text
应用内部直接调用
```

MCP Tool：

```text
通过 MCP Client 发现并调用 MCP Server 暴露的能力
```

业务权限仍然要在 Tool 实现内部校验。

## 二、阅读顺序

1. [MCP Introduction](https://modelcontextprotocol.io/introduction)
2. [MCP Specification](https://modelcontextprotocol.io/specification)
3. [MCP Python SDK](https://github.com/modelcontextprotocol/python-sdk)

阅读时只回答：

- Client 和 Server 分别是什么？
- Tool 的输入 Schema 如何描述？
- Resource 和 Tool 有什么区别？
- 真实系统中权限应该放在哪里？

## 三、动手任务

### 任务 1：选择一个 Tool

建议选择：

```text
search_log
```

原因：

- 只读。
- 风险低。
- 容易用本地数据测试。
- 能体现外部上下文接入。

### 任务 2：实现 MCP Server

Server 至少暴露：

```text
search_log
```

输入：

```json
{
  "run_id": "run-001",
  "query": "NullPointerException",
  "limit": 5
}
```

输出：

```json
{
  "status": "OK",
  "evidence_ids": ["log-001"],
  "items": []
}
```

### 任务 3：增加权限边界

MCP Tool 不能直接读取任意路径。增加：

- 允许的项目列表。
- 允许的数据目录。
- `limit` 上限。
- 内容长度上限。
- `run_id` 校验。

### 任务 4：从 Client 调用

至少完成一次：

```text
Client 连接 Server
  -> 发现 Tool
  -> 传入参数
  -> 获取结构化结果
  -> 写入 Trace
```

如果暂时无法接入完整 Agent，也可以先用独立 Client 调用 MCP Tool，并保留调用记录。

## 四、对比实验

记录普通函数和 MCP Tool 的差异：

| 维度 | 普通函数 | MCP Tool |
|---|---|---|
| 发现方式 | 代码注册 | 协议发现 |
| 调用边界 | 进程内部 | Client / Server |
| 参数描述 | 本地代码 | Tool Schema |
| 权限 | 应用自行实现 | 仍需应用或 Server 实现 |
| 审计 | 自行记录 | 仍需自行记录 |

## 五、测试任务

- MCP Server 能启动。
- Client 能发现 `search_log`。
- 合法参数返回结构正确。
- 未授权项目被拒绝。
- 越界 `limit` 被拒绝。
- 越界路径被拒绝。
- MCP Server 返回错误时 Client 能识别。
- MCP 调用被写入 Trace。

## 六、完成标准

- [ ] 有一个可运行的 MCP Server 或 Tool。
- [ ] 能从 Client 发现并调用 Tool。
- [ ] Tool 有输入和输出 Schema。
- [ ] 有项目、路径和数量限制。
- [ ] 能解释 MCP 与 Agent Framework 的区别。
- [ ] 能解释 MCP 不负责认证、授权和审计。

## 七、今天不要学

- MCP 全部协议细节。
- 自定义传输层。
- 多 Server 编排。
- 复杂 Resource 订阅。

## 八、当天记录

```markdown
## 第 7 天记录

### 我选择封装哪个 Tool？

-

### MCP 比普通函数多解决了什么？

-

### MCP 没有替我解决什么？

-

### 权限校验放在哪一层？

-
```

## 参考资料

- [MCP Introduction](https://modelcontextprotocol.io/introduction)
- [MCP Specification](https://modelcontextprotocol.io/specification)
- [MCP Python SDK](https://github.com/modelcontextprotocol/python-sdk)

