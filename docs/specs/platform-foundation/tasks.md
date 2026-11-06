# Platform Foundation Tasks

状态：Review

| ID | Task | 可观察行为 | 修改范围 | 验收 | 状态 |
|---|---|---|---|---|---|
| FND-01 | 创建 Maven 工程骨架 | `mvn test` 可执行 | `pom.xml`、工程目录、基础配置 | 构建成功 | TODO |
| FND-02 | 提供应用启动和健康检查 | 应用启动后健康接口返回 200 | 启动类、健康接口、配置 | MockMvc 和手工请求 | TODO |
| FND-03 | 实现 Trace ID 和统一错误 | 请求和错误响应都带 Trace ID | 过滤器、异常处理、错误模型 | 单元测试和 Web 测试 | TODO |
| FND-04 | 接入 Flyway 与本地基础设施 | 本地启动后迁移可执行 | 迁移目录、Compose、配置 | 集成测试和启动日志 | TODO |
| FND-05 | 完成测试与运行文档 | 新环境按 README 可启动 | README、示例配置、测试说明 | 文档走查和命令验证 | TODO |

## FND-01

### 目标

建立最小可构建的 Java 21、Spring Boot 3.x、Maven 单模块工程。

### 修改范围

- `pom.xml`
- `src/main/java`
- `src/test/java`
- 基础资源目录

### 不包含

- 业务模块包。
- 数据库业务表。
- 外部模型调用。

### 验收命令

```text
mvn -q test
```

### 依赖

- 基础包名确认。
- Spring Boot 版本确认。

## FND-02

### 目标

应用可启动，并提供稳定的健康检查接口。

### 修改范围

- 启动类。
- 健康检查 Controller。
- 基础应用配置。

### 验收

```text
GET /api/v1/health
```

返回 200，响应包含应用状态。

### 依赖

- FND-01。

## FND-03

### 目标

每个请求具备 Trace ID，错误使用统一结构返回。

### 修改范围

- Trace Filter。
- 全局异常处理。
- 错误模型。
- 相关测试。

### 验收

- 请求未传 Trace ID 时自动生成。
- 合法 Trace ID 被复用。
- 错误响应包含 `code`、`message`、`traceId`。
- 5xx 不泄露堆栈。

### 依赖

- FND-02。

## FND-04

### 目标

建立数据库迁移和本地基础设施启动方式。

### 修改范围

- Flyway 配置。
- 空基线迁移。
- MySQL 和 Redis Compose。
- 本地示例配置。

### 验收

- 容器启动成功。
- Flyway 执行成功。
- 无数据库业务表时迁移处于可扩展基线。

### 依赖

- FND-01。
- Docker 环境确认。

## FND-05

### 目标

开发者按 README 可以构建、测试和启动项目。

### 修改范围

- README。
- `application-local.example.yml`。
- 测试和环境说明。

### 验收

- 文档命令与实际工程一致。
- 不包含真实密钥。
- 新环境可按步骤完成首次启动。

### 依赖

- FND-01 至 FND-04。
