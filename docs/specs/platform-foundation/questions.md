# Platform Foundation Questions

状态：Review

## 阻断问题

| ID | 问题 | 影响 | 建议 | 状态 |
|---|---|---|---|---|
| Q-01 | Java 基础包名使用什么 | 影响工程目录和后续包名 | 使用 `com.lusheng.daogou` | OPEN |
| Q-02 | Spring Boot 精确版本使用哪个 | 影响依赖兼容和构建 | 选择当前稳定 3.x 版本并在 FND-01 锁定 | OPEN |
| Q-03 | Foundation 阶段是否启动 MySQL 和 Redis 容器 | 影响 FND-04 范围和本地依赖 | 两者都启动，Milvus 后置 | OPEN |

## 非阻断问题

| ID | 问题 | 影响 | 建议 | 状态 |
|---|---|---|---|---|
| Q-04 | 是否使用 Maven Wrapper | 影响新环境构建一致性 | 使用并提交 Wrapper 配置 | OPEN |
| Q-05 | Actuator 是否对外暴露 | 影响安全面和运维说明 | 仅暴露 health 和 info | OPEN |

## 已关闭问题

暂无。
