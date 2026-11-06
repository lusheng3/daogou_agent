# Agent Entry

编码前按顺序阅读：

1. `docs/ai-coding.md`
2. `docs/project.md`
3. `docs/architecture.md`
4. `docs/progress.md`
5. 当前模块的 `docs/specs/<module>/` 下全部文件

## Non-negotiable Rules

- 没有已确认的 Spec，不写业务代码。
- 一次只实现一个 Task，一个 Task 对应一个可验证行为。
- 只修改当前 Task 范围内的代码和文档。
- 关键问题未关闭时停止实现，并把问题写入 `questions.md`。
- 不静默改变需求、接口、数据模型、事务边界或幂等规则。
- 完成任务后运行 `tasks.md` 指定的测试和验收命令。
- 完成任务后更新 `docs/progress.md`；重要取舍更新 `docs/decisions.md`。
- 一个 Task 一个 Commit，不把无关重构混入当前提交。
- 不在仓库中提交密钥、Token、密码、个人数据和本地环境配置。
- 不把未实现的能力描述为已完成，不编造测评指标。

## Stop Conditions

出现以下任一情况时停止编码：

- Spec 中存在阻断级未决问题。
- 发现需求与架构冲突。
- 需要跨模块改动但 Spec 未覆盖。
- 验收条件无法执行。
- 测试失败原因无法定位。
