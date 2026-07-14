# pi-subagents 使用入门与实践

## 写在前面

你正在改一段核心代码。你心里清楚：这次改动会影响三个模块，最好有人先帮你理清楚依赖关系；改完之后，最好有人从正确性、测试覆盖和代码整洁三个角度各审一遍；如果改动太大，你还想在动手之前先拿一个第二意见。可你自己就是当前会话里唯一的“大脑”——上下文已经越来越长，思考开始变得拥挤。

这正是 `pi![[tutorial]]subagents` 要解决的问题。它不是把一个更贵的模型叫来替你写代码，而是让你在同一个项目里启动多个**专注的子代理**（subagent）：每个子代理有自己的任务、自己的上下文窗口和自己的生命周期。你仍然是大管家，负责决策与合成；子代理是你的侦察兵、规划师、工人和审查员。

读完本教程，你将能够：

- 判断一个任务是否值得拆给子代理。
- 根据场景选择合适的内置代理。
- 写出单代理、并行、链式、异步等可运行的 `subagent(...)` 示例。
- 理解 `fresh` 与 `fork` 的区别，避免上下文污染。
- 使用 `wait()`、`status`、`doctor` 等工具编排和诊断子代理运行。

---

## 第1章 当一次思考装不下整个任务

### 1.1 一次大改引发的困境

想象你要把项目里的认证模块从 Session-Cookie 改成 JWT。这件事至少包含四件不同性质的工作：

1. **侦察**：摸清当前认证流程涉及哪些文件、函数和数据流。
2. **调研**：确认 JWT 的最佳实践、库版本和常见陷阱。
3. **规划**：把“改成 JWT”拆成若干可验证的小步骤。
4. **实施与审查**：写代码，并检查正确性、测试和安全边界。

如果全部塞进同一个对话，你会遇到三个问题：

- **上下文拥堵**：侦察阶段的细节会挤占实施阶段的思考空间。
- **角色混淆**：同一会话既要全局规划，又要逐行审查，容易顾此失彼。
- **反馈滞后**：你不得不等全部说完，才能进入审查阶段。

子代理的思路是：**把不同性质的工作交给不同的专注会话去做**。

![图 1.1：父会话启动多个子代理，每个子代理拥有独立的任务与上下文。](visuals/chapter-01.svg)

### 1.2 子代理不是“另一个模型”，而是“另一组上下文”

一个常见误解是：子代理就是换了个更厉害的模型。事实上，子代理的核心价值在于**隔离**：

- 每个子代理运行在独立的 `pi` 子进程中。
- 它们只看到你给它们的任务和相关文件，而不是整个对话历史（除非你显式选择继承）。
- 你可以同时启动多个子代理，它们之间互不阻塞。

这意味着你可以让 `reviewer` 不带任何先入为主地审查代码，也可以让 `worker` 只专注于你批准的计划，而不被之前的讨论带偏。

### 1.3 什么时候该拆，什么时候不该拆

| 适合拆给子代理 | 不必拆，自己继续即可 |
|---|---|
| 需要多角度审查（正确性、测试、安全） | 只有一行改动，上下文清晰 |
| 需要先侦察代码再规划 | 任务可以在当前会话三句话内完成 |
| 需要后台运行长时间任务 | 需要与用户进行密集、连续的交互 |
| 需要第二意见再做决策 | 你已经有明确答案，只差执行 |

> **检查点 1.1**：你当前的任务更像“一次性小修改”还是“需要多角色协作的复杂任务”？如果是后者，请列出至少两个可以由不同代理承担的角色。

---

## 第2章 先认人：八个内置代理

`pi-subagents` 安装后自带八个内置代理。你不需要先创建它们，直接按名字调用即可。

### 2.1 侦察型：scout 与 researcher

- **`scout`**：快速扫描本地代码库，回答“这个项目里认证逻辑在哪里”“有哪些文件会受影响”之类的问题。它适合你不熟悉代码时第一步使用。
- **`researcher`**：专门做外部调研，查官方文档、论文、版本说明、最佳实践。它适合“JWT 应该怎么配”“这个库的最新 API 是什么”之类的问题。

### 2.2 规划与实施型：planner 与 worker

- **`planner`**：根据已有上下文生成可执行的计划。它只读不写，适合在实施前把思路结构化。
- **`worker`**：真正动手改代码。它应该在计划被批准后再启动，并且会把改动汇报给你。

### 2.3 审查与决策型：reviewer 与 oracle

- **`reviewer`**：审查代码或计划，指出问题并可能做小幅修复。它是你最常用的“第二双眼睛”。
- **`oracle`**：在动手之前给出第二意见，挑战假设、评估风险。它通常不编辑文件，只提供建议。

### 2.4 通用型：context-builder 与 delegate

- **`context-builder`**：在规划前做更强的上下文交接，输出 `context.md`、`meta-prompt.md` 等文件，适合大型或跨模块任务。
- **`delegate`**：轻量级通用委派，当你只是想把某件事扔给一个子会话处理时用它。

### 2.5 一张选型速查表

![图 2.1：根据任务类型选择内置代理的决策树。](visuals/chapter-02.svg)

| 你的需求 | 首选代理 | 次选 |
|---|---|---|
| 我不熟悉代码 | scout | context-builder |
| 我需要外部事实 | researcher | — |
| 我要一个可执行计划 | planner | context-builder |
| 我要改代码 | worker | — |
| 我要审查改动 | reviewer | oracle |
| 我要决策前的第二意见 | oracle | reviewer |
| 我只是想丢给另一个会话 | delegate | — |

> **常见误区**：让 `worker` 边规划边实现。更好的做法是先让 `planner` 输出计划，你确认后再启动 `worker`。

> **检查点 2.1**：如果你要调查“为什么这个 API 在 v3 和 v4 行为不同”，应该选哪个代理？如果要基于现有代码写迁移计划，又该选哪个？

---

## 第3章 三种基本调用形态

`subagent(...)` 是 `pi-subagents` 提供的工具调用。它的核心模式有三种：single（单代理）、parallel（并行）和 chain（链式）。

![图 3.1：单代理、并行、链式三种调用形态对比。](visuals/chapter-03.svg)

### 3.1 单代理：把一件事交给一个专家

最简单的形态。你把一个具体任务交给一个代理，等待它返回结果。

```typescript
// 示例 3.1：让 reviewer 审查当前 diff
subagent({
  agent: "reviewer",
  task: "审查当前工作区的 git diff，关注空指针检查、边界条件和错误处理。不要修改文件，只返回发现的问题。"
})
```

适用场景：

- 只需要一个专家视角。
- 任务边界清晰，不需要多步骤协作。

### 3.2 并行：多个人同时看不同角度

当你需要从多个独立角度同时审查或调研时，用 `tasks` 数组启动并行任务。

```typescript
// 示例 3.2：并行多角度审查
subagent({
  tasks: [
    {
      agent: "reviewer",
      task: "从正确性角度审查当前 diff：逻辑错误、并发问题、边界条件。",
      output: "review-correctness.md"
    },
    {
      agent: "reviewer",
      task: "从测试角度审查当前 diff：是否缺少单元测试、测试是否覆盖边界。",
      output: "review-tests.md"
    },
    {
      agent: "reviewer",
      task: "从简洁性角度审查当前 diff：是否有重复代码、过度设计、命名问题。",
      output: "review-simplicity.md"
    }
  ],
  concurrency: 3,
  context: "fresh"
})
```

适用场景：

- 多角度审查。
- 同时调研多个独立主题。
- 多个模块的并行侦察。

> **注意**：并行任务的输出路径不能冲突，每个任务应写自己的文件。

### 3.3 链式：上一步的输出是下一步的输入

当任务有先后依赖时，用 `chain`。后一步可以通过 `{previous}` 引用前一步的输出。

```typescript
// 示例 3.3：侦察 → 计划 → 实现
subagent({
  chain: [
    {
      agent: "scout",
      task: "梳理当前项目的认证流程：列出相关文件、入口函数、数据流和潜在风险点。"
    },
    {
      agent: "planner",
      task: "根据 {previous} 输出一份从 Session-Cookie 迁移到 JWT 的可执行计划，包含验证步骤。"
    },
    {
      agent: "worker",
      task: "按照 {previous} 批准的计划实施迁移。每完成一步运行一次测试，并汇报结果。"
    }
  ]
})
```

适用场景：

- 侦察 → 计划 → 实施。
- 任何有明确先后依赖的流程。

### 3.4 模板变量的使用

链式步骤中可以使用这些模板变量：

| 变量 | 含义 |
|---|---|
| `{task}` | 原始任务字符串 |
| `{previous}` | 上一步的输出 |
| `{chain_dir}` | 链的共享目录 |
| `{outputs.name}` | 命名输出的引用（需要配合 `as` 使用） |

如果你给某一步起了别名 `as: "plan"`，后面可以这样引用：

```typescript
// 示例 3.4：使用命名输出
subagent({
  chain: [
    {
      agent: "planner",
      task: "制定重构计划",
      as: "plan",
      output: "plan.md"
    },
    {
      agent: "worker",
      task: "按照以下计划实施：\n{outputs.plan}\n\n不要偏离计划。",
      output: "implementation.md"
    }
  ]
})
```

> **检查点 3.1**：假设你要同时分析前端和后端的代码质量，应该用哪种调用形态？如果要先分析前端再分析后端，又该用哪种？

---

## 第4章 异步、上下文与隔离

### 4.1 同步 vs 异步：`async: true`

默认情况下，`subagent(...)` 是同步的：父会话会等待子代理完成后才继续。如果你希望子代理在后台运行，你做别的事，就加上 `async: true`。

```typescript
// 示例 4.1：后台运行测试套件
subagent({
  agent: "worker",
  task: "运行完整测试套件，并保存结果到 test-results.md。",
  async: true,
  output: "test-results.md"
})
```

异步运行的优势：

- 你可以继续本地检查、准备验证或启动其他子代理。
- 长时间任务（如完整测试、大规模重构）不会阻塞对话。

### 4.2 `wait()`：orchestrator 的阻塞与继续

当你启动了异步子代理，并且没有别的独立工作要做时，不要轮询状态，应该调用 `wait()`：

```typescript
// 示例 4.2：等待异步子代理完成
wait({ all: true, timeoutMs: 300000 })  // 最多等 5 分钟
```

| 调用方式 | 行为 |
|---|---|
| `wait()` | 任意一个活跃异步运行完成或需要关注时返回 |
| `wait({ all: true })` | 所有活跃运行都完成或某一运行需要关注时返回 |
| `wait({ id: "..." })` | 等待指定运行 |
| `wait({ timeoutMs: N })` | 最多等待 N 毫秒，运行继续后台执行 |

> **注意**：在技能或非交互式运行中，如果你直接结束回合而不 `wait()`，异步子代理可能被遗弃。

### 4.3 `fresh` 与 `fork`：要干净的上下文还是继承的历史？

子代理启动时可以选择上下文策略：

| 策略 | 含义 | 适合场景 |
|---|---|---|
| `context: "fresh"` | 子代理获得一个干净的上下文，只带任务描述 | 审查、调研、侦察 |
| `context: "fork"` | 子代理从父会话当前状态分支出一个继承历史的新会话 | oracle 咨询、worker 实施 |

```typescript
// 示例 4.3：审查用 fresh，避免历史偏见
subagent({
  agent: "reviewer",
  task: "审查 src/auth.ts 的空指针风险。",
  context: "fresh"
})

// 示例 4.4：决策咨询用 fork，保留上下文
subagent({
  agent: "oracle",
  task: "基于我们刚才的讨论，挑战这个迁移计划的假设，指出遗漏的风险。",
  context: "fork"
})
```

![图 4.1：fresh 与 fork 两种上下文策略的差异。](visuals/chapter-04.svg)

> **注意**：`planner`、`worker`、`oracle` 在省略 `context` 时默认使用 `fork`。如果你明确需要干净上下文，要显式写 `context: "fresh"`。

### 4.4 `worktree: true`：多个写者时的隔离

如果你确实需要多个子代理同时修改代码，给它们各自的 git worktree：

```typescript
// 示例 4.5：并行 worker 在独立 worktree 中工作
subagent({
  tasks: [
    { agent: "worker", task: "在独立分支实现前端改动" },
    { agent: "worker", task: "在独立分支实现后端改动" }
  ],
  worktree: true
})
```

> **常见误区**：默认情况下不要让多个 worker 同时写同一个工作区。要么只保留一个写者，要么用 `worktree: true` 隔离。

> **检查点 4.1**：你启动了一个异步 `researcher` 去查文档，同时想本地检查代码。应该怎么写？完成后又该如何等待它？

---

## 第5章 四个可复制的工作流

这一章把前面学到的调用形态组合成可以直接复制的模式。

### 5.1 侦察 → 计划 → 实现

这是最常见的完整链路，适合你不熟悉代码又要做较大改动的情况。

```typescript
// 示例 5.1：侦察 → 计划 → 实现
subagent({
  chain: [
    {
      agent: "scout",
      task: "梳理 src/auth/ 目录下的认证流程，列出关键文件、函数调用链和风险点。",
      output: "auth-context.md"
    },
    {
      agent: "planner",
      task: "阅读 {previous}，制定从 Session-Cookie 迁移到 JWT 的分步计划。",
      output: "auth-plan.md"
    },
    {
      agent: "worker",
      task: "阅读 {previous} 中的计划，按步骤实施。每完成一步运行相关测试，并汇报通过情况。",
      output: "auth-implementation.md"
    }
  ]
})
```

![图 5.1：侦察 → 计划 → 实现链式工作流。](visuals/chapter-05.svg)

### 5.2 并行多角度审查

改完代码后，同时从多个角度审查，再由父会话合成反馈。

```typescript
// 示例 5.2：并行多角度审查
subagent({
  tasks: [
    {
      agent: "reviewer",
      task: "审查当前 diff 的正确性：逻辑、边界、并发、错误处理。",
      output: "review-correctness.md",
      outputMode: "file-only"
    },
    {
      agent: "reviewer",
      task: "审查当前 diff 的测试覆盖：是否新增/更新测试，边界是否覆盖。",
      output: "review-tests.md",
      outputMode: "file-only"
    },
    {
      agent: "reviewer",
      task: "审查当前 diff 的简洁性：重复、命名、过度设计。",
      output: "review-simplicity.md",
      outputMode: "file-only"
    }
  ],
  concurrency: 3,
  context: "fresh"
})
```

![图 5.2：并行多角度审查后由父会话合成反馈。](visuals/chapter-05b.svg)

> **`outputMode: "file-only"` 的作用**：当审查结果很长时，子代理只返回文件路径和摘要，而不是把全文塞回父会话。

### 5.3 审查 → 修复循环

让 `worker` 改完后再让 `reviewer` 审，审完再让 `worker` 修，最多三轮。

```typescript
// 示例 5.3：审查-修复循环（第一轮）
subagent({
  chain: [
    {
      agent: "worker",
      task: "实现已批准的重构计划。",
      output: "impl-v1.md"
    },
    {
      agent: "reviewer",
      task: "审查 {previous} 产生的 diff。只返回必须现在修复的问题。",
      context: "fresh",
      output: "review-v1.md"
    },
    {
      agent: "worker",
      task: "根据 {previous} 的审查结果修复代码。只修复被标记为必须修复的问题。",
      output: "impl-v2.md"
    }
  ]
})
```

> **实践建议**：不要把“可选优化”也塞进修复轮次，否则循环会无限延长。明确告诉 reviewer 只返回“值得现在修复”的问题。

### 5.4 oracle 审核 → worker 实现

在做大决策前，先让 `oracle` 给第二意见，你确认方向后再让 `worker` 动手。

```typescript
// 示例 5.4：oracle 先审核，worker 后实现
subagent({
  chain: [
    {
      agent: "oracle",
      task: "审查以下重构计划，挑战假设并指出遗漏风险。不要修改文件。\n\n计划：把认证从 Session 改成 JWT。",
      context: "fork",
      output: "oracle-feedback.md"
    },
    {
      agent: "worker",
      task: "在采纳 oracle 反馈后实施重构。如果遇到未批准的产品或架构决策，立即通过 contact_supervisor 请示。",
      output: "impl.md"
    }
  ]
})
```

> **关键约束**：`worker` 不应该在未经你批准的情况下做产品或架构决策。遇到这类问题，它应该通过 `contact_supervisor` 联系你。

> **检查点 5.1**：你刚改完一个功能，想先让三个人分别看正确性、测试和简洁性，然后决定哪些反馈要修复。这是哪个工作流？请写出对应的调用形态名称。

---

## 第6章 模型覆盖、管理与排错

### 6.1 默认模型与 `agentOverrides`

内置代理默认继承当前 Pi 会话的模型。你可以在不同层级覆盖：

| 层级 | 设置位置 | 影响范围 |
|---|---|---|
| Pi 默认模型 | 当前会话 | 所有未显式设置模型的调用 |
| `subagents.defaultModel` | `~/.pi/agent/settings.json` 或 `.pi/settings.json` | 所有子代理 |
| `subagents.agentOverrides.<name>.model` | 同上 | 特定角色 |
| 单次调用 `model` | `subagent({ model: ... })` | 仅本次调用 |

```json
// 示例 6.1：设置 reviewer 用更强的模型
{
  "subagents": {
    "agentOverrides": {
      "reviewer": {
        "model": "anthropic/claude-sonnet-4",
        "thinking": "high",
        "fallbackModels": ["openai/gpt-5-mini"]
      }
    }
  }
}
```

![图 6.1：模型覆盖优先级，从默认模型到单次调用参数。](visuals/chapter-06.svg)

### 6.2 查看可用代理：`list`

```typescript
// 示例 6.2：列出所有可用代理和链
subagent({ action: "list" })
```

这会返回当前环境中可发现的代理，包括内置、用户自定义和项目自定义代理。优先级为：项目 &gt; 用户 &gt; 内置。

### 6.3 查看运行状态：`status`、`fleet`、`transcript`

```typescript
// 示例 6.3：查看所有活跃异步运行
subagent({ action: "status" })

// 查看舰队视图
subagent({ action: "status", view: "fleet" })

// 查看某个运行的实时输出
subagent({ action: "status", id: "abc123", view: "transcript" })
```

### 6.4 诊断：`doctor`

当子代理启动失败、intercom 消息没收到或状态异常时，先跑诊断：

```typescript
// 示例 6.4：运行诊断
subagent({ action: "doctor" })
```

对应的斜杠命令是 `/subagents-doctor`。

> **常见排错清单**：
> - `fork` 失败？检查当前父会话是否已持久化。
> - 子代理看不到文件？检查 `cwd` 是否设置正确。
> - 异步运行没通知？确认是否调用了 `wait()` 或会话是否仍在运行。
> - 模型 ID 报错？用模糊匹配试试更短的名称，或运行 `/subagents-models` 查看可用映射。

> **检查点 6.1**：你想让 `oracle` 总是用最贵的模型，而 `worker` 用默认模型。应该在哪个配置文件里写什么？

---

## 第7章 练习：为一次真实重构设计子代理方案

![图 7.1：把真实任务拆成可由不同代理承担的步骤。](visuals/chapter-07.svg)

### 7.1 任务卡

假设你的团队要把一个 Python 项目里的 `requests` 调用全部迁移到 `httpx`，同时保持现有行为不变。请设计一个使用 `pi-subagents` 的执行方案，要求：

1. 先了解当前 `requests` 的使用范围。
2. 调研 `httpx` 与 `requests` 的关键差异。
3. 制定迁移计划。
4. 实施迁移。
5. 从正确性和测试覆盖两个角度审查结果。

### 7.2 自检清单

- [ ] 我明确列出了每个步骤由哪个代理承担。
- [ ] 我说明了每一步使用 `fresh` 还是 `fork`。
- [ ] 我说明了哪些步骤可以并行、哪些必须串行。
- [ ] 我说明了如何处理未批准的决策。
- [ ] 我写出了至少一段可运行的 `subagent(...)` 示例。

### 7.3 参考答案要点

一个可行的方案：

1. **scout**（fresh）：扫描代码库，列出所有 `requests` 使用点、封装函数和测试文件。
2. **researcher**（fresh）：调研 `httpx` 与 `requests` 的 API 差异、异步支持和常见迁移坑。
3. **planner**（fork 或 fresh）：基于 scout 和 researcher 的输出，制定迁移计划。
4. **worker**（fork）：按计划实施迁移，每改完一个模块运行一次测试。
5. **reviewer × 2**（fresh，并行）：一个审正确性，一个审测试覆盖。
6. **worker**（fork）：根据审查反馈做最终修复。

可运行的核心片段：

```typescript
subagent({
  chain: [
    {
      agent: "scout",
      task: "扫描当前 Python 项目，找出所有使用 requests 的文件、函数和测试。",
      output: "requests-usage.md"
    },
    {
      agent: "researcher",
      task: "调研 httpx 与 requests 的关键 API 差异、迁移注意事项和常见坑。",
      output: "httpx-research.md"
    },
    {
      agent: "planner",
      task: "阅读 {outputs.requests-usage} 和 {outputs.httpx-research}，制定分模块迁移计划。",
      output: "migration-plan.md"
    },
    {
      agent: "worker",
      task: "按照 {previous} 中的迁移计划实施。每完成一个模块运行 pytest，并汇报结果。",
      output: "migration-impl.md"
    }
  ]
})
```

---

## 延伸阅读与参考

- [pi-subagents GitHub README](https://github.com/nicobailon/pi-subagents/blob/main/README.md)
- [pi-subagents Package 页面](https://pi.dev/packages/pi-subagents)
- pi-subagents 本地技能文档：`pi-subagents/skills/pi-subagents/SKILL.md`

> 如果你在真实项目中遇到困难，可以通过 `subagent({ action: "doctor" })` 或 `/subagents-doctor` 先做诊断。也可以把具体任务和当前代码发给我，我们一起把它拆成合适的子代理方案。
