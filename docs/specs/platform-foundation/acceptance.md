# Platform Foundation Acceptance

状态：Review

## 1. 前置条件

- JDK 21 可用。
- Maven 或 Maven Wrapper 可用。
- Docker 可用于基础设施验收。
- 数据库和缓存使用示例配置中的本地端口。

## 2. 单元测试

- Trace ID 合法输入被复用。
- Trace ID 非法输入被替换。
- Trace ID 长度和字符受限。
- 业务错误映射到稳定错误码。
- 未知异常映射为通用 5xx 错误。

## 3. Web 测试

- `GET /api/v1/health` 返回 200。
- 响应包含 `X-Trace-Id`。
- 参数校验失败返回统一错误结构。
- 错误响应不包含堆栈和内部路径。
- 未传 `X-Trace-Id` 的请求会生成新 ID。

## 4. 集成测试

- MySQL 容器启动后可执行 Flyway。
- Flyway 重复启动不会重复执行同一迁移。
- Redis 连接配置错误时应用仍可启动，并明确标记依赖异常。

## 5. 手工验收

1. 按 README 启动基础设施。
2. 启动 Spring Boot。
3. 请求 `/api/v1/health`。
4. 检查响应和 `X-Trace-Id`。
5. 触发一个参数错误并检查错误结构。
6. 查看日志中的 Trace ID。

## 6. Definition of Done

- `FND-01` 至 `FND-05` 全部完成。
- Maven 测试通过。
- 手工验收通过。
- README 与示例配置可执行。
- `progress.md` 已更新。
- 未引入任何业务模块代码。
