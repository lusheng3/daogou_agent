# 架构说明

## 1. 总体方案

系统采用模块化单体，内部由稳定 Workflow 和受限 ReAct Agent 组成。

```text
Client
-> API / SSE
-> Workflow
   -> Risk Control
   -> Memory Selection
   -> Query Rewrite
   -> Intent Routing
   -> Context Assembly
-> ReAct Runtime
   -> Tool Registry
   -> Product / Knowledge / Cart Tools
-> Response Builder
-> Persistence / Trace / Evaluation
```

Workflow 负责必须稳定执行的步骤。ReAct 只负责需要模型判断的工具选择、证据组织和回答生成。

## 2. 模块边界

| 模块 | 职责 | 不负责 |
|---|---|---|
| `platform-foundation` | 工程基础、错误、Trace、配置、测试基础 | 业务导购逻辑 |
| `product-catalog` | 商品、SKU、价格、库存和上下架 | 对话和模型决策 |
| `conversation` | session、message、display、SSE 事件 | 商品推荐策略 |
| `memory` | 历史筛选、长期偏好、历史商品定位 | 数据库基础建设 |
| `query-rewrite` | 指代、省略、否定和假设补全 | 意图业务分支 |
| `intent` | domain、action 和场景识别 | 工具执行 |
| `context-assembly` | 规则、记忆、工具和预算组装 | 模型调用循环 |
| `agent-runtime` | ReAct 循环、Observation 和收束 | 具体工具实现 |
| `tool-platform` | 注册、Schema 校验、执行和适配 | 业务规则本身 |
| `rag` | 切片、召回、融合、重排和父段回取 | 价格库存实时校验 |
| `cart-operation` | 加购、事务和幂等 | 商品推荐 |
| `evaluation` | 测试集、Trace 归因和评分 | 线上请求主流程 |
| `web-demo` | 最小聊天和商品卡界面 | 业务规则 |

## 3. 主数据对象

### conversation

- `session`
- `message`
- `display`
- `display_item`

### catalog

- `product`
- `sku`
- `inventory`
- `price`

### memory

- `preference`
- `memory_selection`

### agent

- `agent_run`
- `agent_step`
- `tool_call`
- `tool_observation`

### commerce

- `operation`
- `cart`
- `cart_item`

字段设计由对应模块 Spec 定义。

## 4. 核心链路

### 4.1 请求进入

1. 生成或透传 `trace_id`。
2. 校验用户身份和会话归属。
3. 保存用户消息。
4. 执行确定性风控。

### 4.2 上下文准备

1. 读取最近消息和展示记录。
2. 筛选相关历史。
3. 读取长期偏好。
4. 改写 Query。
5. 判断 domain 和 action。
6. 加载业务规则、动作规则和工具集合。

### 4.3 Agent 执行

1. 组装 system、user、memory 和工具定义。
2. 模型返回工具动作或最终回答。
3. 程序校验工具是否允许、参数是否合法。
4. 执行工具并追加 Observation。
5. 达到调用预算后只生成最终回答。

### 4.4 结果返回

1. 推送回答和商品卡事件。
2. 保存回答、display 和操作状态。
3. 记录 Trace。
4. 异步更新长期偏好。

## 5. 一致性边界

- 用户消息和 agent run 创建在一个事务内。
- 加购的商品定位、库存校验、操作记录和购物车更新必须明确事务边界。
- `operation_id` 由服务端生成，客户端重试时复用。
- SSE 只负责展示，不作为唯一持久化来源。
- 最终一致性允许偏好异步更新，但不允许购物车写入丢失。

## 6. 外部依赖

- MySQL：业务事实和持久化记录。
- Redis：热数据、短期状态和限流预留。
- Milvus：知识块向量索引。
- LLM Provider：Generative Model 和结构化判断模型。
- 对象存储：后续用于上传资料，MVP 可后置。

## 7. 部署形态

第一阶段为一个 Spring Boot 进程，配合 MySQL、Redis 和 Milvus。

不在第一阶段拆分微服务，不允许业务模块直接共享数据库表的写入职责。

## 8. 架构原则

- 稳定流程由代码控制，开放决策交给模型。
- 业务硬条件优先于语义相关性。
- 答案必须有来源，业务字段必须回源校验。
- 所有模型和工具循环必须有预算。
- 所有写操作必须可重试且不重复写入。
- Trace 先于复杂优化，没有 Trace 不定位模型问题。
