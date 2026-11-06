# Platform Foundation Design

状态：Review

## 1. 方案概览

创建一个单 Maven 模块的 Spring Boot 应用。业务能力通过 Java 包划分，暂不创建 Maven 多模块。

建议基础包名：

```text
com.lusheng.daogou
```

后续业务包按能力拆分：

```text
com.lusheng.daogou
  platform
  common
  catalog
  conversation
  memory
  routing
  agent
  rag
  cart
  evaluation
```

未实现模块暂时不创建空包。

## 2. 工程配置

- Java release：21。
- 构建工具：Maven。
- 框架：Spring Boot 3.x。
- 配置格式：YAML。
- 主配置：`application.yml`。
- 本地配置：`application-local.yml`，不提交。
- 示例配置：`application-local.example.yml`。

精确 Spring Boot 补丁版本在 `FND-01` 实现时查询并锁定。

## 3. 基础依赖

### 必须引入

- Spring Boot Web。
- Spring Boot Validation。
- Spring Boot Actuator。
- Flyway。
- MySQL Driver。
- 测试依赖。

### Foundation 阶段暂不引入

- LLM SDK。
- Milvus SDK。
- LangChain4j。
- 消息队列。
- 前端依赖。

Redis 依赖是否在 Foundation 阶段引入由 `questions.md` 决定。

## 4. API 与错误契约

健康检查：

```text
GET /api/v1/health
```

成功响应至少包含：

- `status`
- `traceId`

统一错误响应：

```json
{
  "code": "VALIDATION_ERROR",
  "message": "Request validation failed",
  "traceId": "request-trace-id",
  "details": [],
  "timestamp": "ISO-8601"
}
```

规则：

- `code` 稳定且可被调用方判断。
- `message` 可读，但不包含内部实现细节。
- `details` 只包含安全字段错误。
- 5xx 错误必须记录完整服务端日志，但响应中只返回通用消息。

## 5. Trace ID

请求进入时：

1. 读取 `X-Trace-Id`。
2. 校验格式和长度。
3. 合法则复用，不合法则重新生成。
4. 写入 MDC。
5. 通过响应头返回。
6. 请求结束后清理 MDC。

Trace ID 只用于请求关联，不替代后续的 `agent_run_id` 和 `task_id`。

## 6. 数据迁移

- 使用 Flyway。
- SQL 放在 `src/main/resources/db/migration`。
- Foundation 只创建迁移机制，不提前创建未确认的业务表。
- 迁移脚本一经共享环境执行，不修改历史版本，只追加新版本。

## 7. 本地基础设施

建议 `compose.yaml` 在 Foundation 阶段只包含：

- MySQL 8。
- Redis 7。

Milvus 在 RAG 模块开始时加入，避免早期资源占用和启动复杂度。

数据库和缓存使用固定端口、数据卷、健康检查和环境变量。

## 8. 测试设计

### 单元测试

- Trace ID 生成和校验。
- 错误码映射。
- 配置绑定。

### Web 测试

- 健康检查返回成功。
- 参数校验错误转换为统一错误结构。
- 响应包含 Trace ID。
- 未处理异常不泄露内部信息。

### 集成测试

- Flyway 在测试数据库中执行。
- MySQL 容器可用时运行持久化基础设施测试。

避免所有测试都依赖 Docker。纯单元测试和 MockMvc 测试必须可直接执行。

## 9. 健康检查

- `/api/v1/health` 检查应用自身状态。
- Actuator 可用于后续详细检查。
- 外部依赖不可用时，主健康状态可标记为 degraded，但应用不应因此无法启动。

## 10. 可观测性

- 使用 SLF4J。
- 每个请求日志包含 `trace_id`。
- 统一错误处理记录错误类型和请求路径。
- 不在日志中记录 Authorization、Cookie、API Key 和完整请求正文。

## 11. 替代方案

### 直接创建 Maven 多模块

未选择：当前边界尚未稳定，会提高构建和重构成本。

### 先引入全部中间件

未选择：Milvus 等依赖不影响 Foundation 验收，提前引入会增加启动和调试成本。

### 使用 Gradle

未选择：当前单人单应用场景下 Maven 更直观，招聘项目也更易解释。

## 12. 待确认问题

见 `questions.md`。
