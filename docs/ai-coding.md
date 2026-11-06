# AI Coding 规范

## 1. 目标

本文档约束 AI Agent 在本仓库中的工作方式。目标不是让 Agent 一次生成更多代码，而是让每次改动范围明确、可验证、可回滚、可积累。

## 2. 基本工作单元

工作单元从大到小分为：

1. 里程碑
2. 模块
3. Task
4. Commit

一个 Task 应满足：

- 对应一个可观察的行为变化。
- 能在一次实现会话内完成。
- 有明确修改范围。
- 有可执行的验收条件。
- 不依赖 Agent 自行猜测业务规则。
- 完成后可以独立提交和回滚。

## 3. 先 Spec 后代码

每个模块编码前必须具备：

- `requirements.md`
- `design.md`
- `tasks.md`
- `questions.md`
- `acceptance.md`

详细要求见 `docs/specs/README.md`。

关键问题未关闭时，不允许进入实现。实现过程中发现 Spec 缺口时，先更新 `questions.md`，确认后再修改 Spec。

## 4. 会话类型

### Spec 会话

- 读取项目基线、架构和已有代码。
- 只输出或修改 Spec。
- 不实现业务代码。

### 实现会话

- 只读取 `AGENTS.md`、当前 Spec、进度文件和相关代码。
- 一次实现一个 Task。
- 改动前确认 Task 范围和验收命令。
- 完成后运行测试并更新进度。

### Review 会话

- 独立检查 Spec、Diff、测试结果和验收条件。
- 重点寻找行为错误、边界遗漏和测试缺口。
- 不延续实现会话的假设。

### 错例分析会话

- 从 Trace、输入、期望结果和实际结果开始。
- 定位最早出错的节点。
- 先补回归用例和规则，再决定修改代码。

## 5. 技术基线

- Java 21。
- Spring Boot 3.x，具体补丁版本在 `platform-foundation` 中锁定。
- Maven。
- 单一 Spring Boot 应用。
- 按业务能力划分包和 Spec，不按 Controller、Service、Repository 建立顶层业务边界。
- 前期不拆微服务，不引入多 Agent 框架。

## 6. 编码约定

- 使用构造器注入，不使用字段注入。
- API 层只负责协议转换、参数校验和响应封装。
- 业务规则放在应用层或领域层，不写在 Controller。
- 外部模型、数据库、缓存和检索系统通过接口隔离。
- 所有跨模块调用使用明确的应用接口或事件，不直接访问其他模块的内部实现。
- 错误必须带稳定错误码，不能只返回自然语言。
- 任何写操作必须明确事务边界和幂等策略。
- Prompt、模型配置、检索参数和数据集必须可版本化。
- 不在日志中输出密钥、完整 Prompt 中的敏感数据或个人信息。

## 7. 测试层级

### 单元测试

验证纯业务规则、参数校验、状态转换和幂等逻辑。

### 组件测试

验证模块内部与数据库、缓存、模型适配器或检索适配器的协作。

### 集成测试

验证 API、持久化、流式事件和外部依赖契约。

### 端到端测评

使用固定场景和业务状态验证推荐、比较、追问和加购结果。

模型输出不稳定时，测试应验证结构化事实、约束和指标，而不是要求逐字一致。

## 8. 提交规则

提交信息使用：

```text
type(scope): summary
```

允许的类型：

- `docs`
- `test`
- `feat`
- `fix`
- `refactor`
- `chore`

示例：

```text
docs(spec): define platform foundation
feat(conversation): persist display order
fix(cart): reuse operation id on retry
```

## 9. 完成定义

一个 Task 只有同时满足以下条件才算完成：

- 实现符合当前 Spec。
- 目标测试通过。
- 没有跳过或删除验收用例。
- `progress.md` 已更新。
- 需要时 `decisions.md` 已记录取舍。
- Commit 不包含无关改动。
