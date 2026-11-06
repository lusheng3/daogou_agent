# 模块 Spec 规则

## 1. 文件结构

每个模块使用固定结构：

```text
docs/specs/<module>/
  requirements.md
  design.md
  tasks.md
  questions.md
  acceptance.md
```

## 2. 编写顺序

1. 写 `requirements.md`。
2. 写 `design.md`。
3. 写 `acceptance.md`。
4. 拆分 `tasks.md`。
5. 关闭 `questions.md` 中的阻断问题。
6. 人工确认后开始实现。

## 3. 状态

每个模块 Spec 使用以下状态：

- `Draft`：正在编写，不能实现。
- `Review`：等待人工确认，不能实现。
- `Approved`：可以拆分并实现 Task。
- `Implementing`：正在实现。
- `Done`：通过验收。
- `Changed`：已实现部分因新需求需要重新确认。

## 4. 模块顺序

| 顺序 | 模块 | 当前状态 |
|---|---|---|
| 1 | `platform-foundation` | Review |
| 2 | `product-catalog` | Not Started |
| 3 | `conversation` | Not Started |
| 4 | `memory` | Not Started |
| 5 | `query-rewrite` | Not Started |
| 6 | `intent` | Not Started |
| 7 | `context-assembly` | Not Started |
| 8 | `agent-runtime` | Not Started |
| 9 | `tool-platform` | Not Started |
| 10 | `rag` | Not Started |
| 11 | `cart-operation` | Not Started |
| 12 | `evaluation` | Not Started |
| 13 | `web-demo` | Not Started |

## 5. 详细程度

- 当前模块必须写完整五份文档。
- 下一个模块可提前写需求草稿。
- 后续模块只在路线图中登记，不在当前阶段编写详细设计。

这样可以避免早期猜测导致大量过时文档。

## 6. Task 规则

`tasks.md` 中的每个 Task 必须包含：

- Task ID。
- 目标和可观察行为。
- 修改范围。
- 验收方法。
- 测试命令。
- 依赖关系。
- 状态。

实现顺序默认为从上到下；存在依赖时必须显式标记。
