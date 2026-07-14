# 让 AI Agent 少犯错：从 AGENTS.md 到 CLAUDE.md 的项目级系统提示词设计教程

你可能已经遇到过这种情况：同一个 AI coding agent，昨天刚被你提醒“这个项目用 pnpm，不要用 npm”，今天又开始运行 `npm install`；你反复说“不要改生成文件”，它还是顺手改了 `dist/`；你希望它每次改完都跑测试，但它经常只说“建议你运行测试”。

这类问题不完全是模型能力问题。很多时候，是项目没有把“新同事上手前必须知道的规则”写成 agent 每次都能看到、能执行、能验证的工作协议。

本教程教你设计这类协议：`AGENTS.md`、`CLAUDE.md`、`GEMINI.md`、`.cursor/rules/*.mdc`、`.github/copilot-instructions.md`、`.instructions.md` 等。读完后，你应该能为自己的项目写出一套简洁、可维护、可迁移的 agent 指令体系，而不是堆一份越来越长、谁也不敢删的“提示词愿望清单”。

---

## 第1章 为什么项目级指令文件不是普通 Prompt

本章目标：先分清三件事——一次性聊天 prompt、agent 的系统提示词、项目级指令文件。只有分清它们，才知道哪些内容该写进 `AGENTS.md` 或 `CLAUDE.md`，哪些应该留在本次任务里。

![第1章：不是普通 Prompt](visuals/chapter-01.svg)

图 1.1：项目级指令文件把临时口头要求变成稳定、可维护的工作协议。

### 1.1 反复解释同一件事，是系统设计问题

如果你每天都在对 agent 说同一句话，这句话就不该只停留在聊天窗口里。

比如这些内容：

- “本项目使用 pnpm，不要用 npm。”
- “后端 API 的输入校验必须用 Zod。”
- “不要改 `generated/` 和 `dist/` 目录。”
- “修改代码前先读相关测试。”
- “完成前至少运行 `pnpm test` 或说明为什么不能运行。”

这些不是一次性任务细节，而是项目的工作规则。人类新同事入职时，你会把它们写进 onboarding 文档；AI agent 参与项目时，也需要一个稳定入口。`AGENTS.md`、`CLAUDE.md` 等文件就是这个入口。

要注意：这些文件不是“魔法咒语”。它们通常会被工具加载到模型上下文中，影响 agent 的行为，但不等于强制执行。真正必须阻止的事情，例如删除生产数据、泄露密钥、绕过审批，应该交给权限设置、hooks、guardrails、CI 或服务端校验。

> 观察点：如果一个规则只是“希望模型尽量这样做”，可以写进指令文件；如果它是“绝不能发生”，就不要只靠自然语言提示词。

### 1.2 一次性 prompt、系统 prompt、项目级指令文件的差别

可以把三者理解成不同层级的输入：

| 类型 | 作用 | 适合放什么 | 不适合放什么 |
| --- | --- | --- | --- |
| 一次性用户 prompt | 本次任务 | “帮我修复登录页报错”、本次日志、本次需求 | 永久项目规则 |
| 系统提示词 | agent 的基础身份和行为边界 | 角色、工具策略、输出格式、安全红线 | 某个仓库今天的具体文件 |
| 项目级指令文件 | 当前项目的长期上下文 | 架构、命令、测试、代码规范、常见坑 | 密钥、临时任务、过期细节 |

项目级指令文件常常不是底层 API 意义上的 system message。很多工具会把它作为上下文或用户级记忆加载进去。这个差别很重要：它能提高稳定性，但不是安全边界。

所以你设计它时，要同时满足两件事：

1. **像系统提示词一样稳定**：每次开工都应该成立。
2. **像项目文档一样具体**：能告诉 agent 这个仓库到底怎么工作。

### 1.3 指令文件是“可见的工作协议”，不是魔法强制器

一个好的 `AGENTS.md` 不是写给模型看的神秘文本，而是写给“任何会在这个项目中行动的自动化助手”看的工作协议。它应该让 agent 快速回答五个问题：

1. 这个项目是什么？
2. 代码在哪里？
3. 安装、运行、测试命令是什么？
4. 修改代码时必须遵守哪些非显而易见的规则？
5. 什么时候算完成，什么时候必须停下来问人？

坏的指令文件通常长这样：

```markdown
# Rules
- Be careful.
- Write clean code.
- Follow best practices.
- Make sure everything works.
```

这几句话看似正确，但几乎没有执行价值。agent 不知道“careful”具体是什么意思，也不知道“everything works”要跑哪个命令验证。

更好的写法是：

```markdown
# Project Rules
- Package manager: use `pnpm`, not `npm` or `yarn`.
- Before editing an API handler, read the matching test file under `tests/api/`.
- Do not modify generated files under `src/generated/`; update the schema and run `pnpm generate` instead.
- Before finishing a code change, run `pnpm lint` and the smallest relevant test command. If a command cannot run, explain why and what you would run locally.
```

这里每条规则都能被检查，也能指导下一步行动。

常见误区：把项目级指令文件写成“团队价值观”。价值观可以保留在 README 或工程文化文档里，但 agent 更需要命令、边界、路径、验证方式和具体例外。

练习：打开你最近的一个项目，列出你在过去一周对 AI 助手重复说过两次以上的话。把其中“每次都成立”的内容标记出来，它们就是指令文件的候选规则。

检查点：你能否用一句话说明 `AGENTS.md` 的目的？一个可用答案是：它是项目写给 coding agent 的 README，告诉 agent 在这个仓库里应该如何行动、验证和停止。

---

## 第2章 先分层：哪些规则放哪里

本章目标：学会按作用域放规则。很多指令文件混乱，不是因为内容太少，而是因为组织级政策、个人偏好、项目命令、子目录规范和本次任务材料全塞在一起。

![第2章：规则分层地图](visuals/chapter-02.svg)

图 2.1：好规则先有清楚作用域；越具体的规则越靠近它影响的文件。

### 2.1 五层规则：组织、个人、项目、子目录、任务

设计 agent 指令文件时，先问：“这条规则应该影响多大范围？”

| 层级 | 示例 | 推荐位置 |
| --- | --- | --- |
| 组织层 | 禁止泄露客户数据；所有生产变更必须审批 | 企业策略、系统级规则、强制设置、hooks |
| 个人层 | 回复我时用中文；解释要简短 | 用户级规则，如 `~/.claude/CLAUDE.md`、用户规则 |
| 项目层 | 本仓库用 pnpm；API 在 `src/api/`；测试命令是什么 | 根目录 `AGENTS.md`、`CLAUDE.md`、`.github/copilot-instructions.md` |
| 子目录层 | `frontend/` 用 React 约定；`backend/` 用服务层模式 | 嵌套 `AGENTS.md`、`.claude/rules/`、`.cursor/rules/`、`.instructions.md` |
| 任务层 | 今天要修哪个 bug；本次日志；本次验收标准 | 当前聊天 prompt 或 issue 描述 |

一条规则越具体，越应该靠近它影响的文件。比如“所有 React 组件用 named export”不需要影响数据库迁移目录；它更适合放在 `frontend/AGENTS.md` 或针对 `src/components/**/*.tsx` 的路径规则里。

### 2.2 Durable vs per-request：常驻规则与本次任务信息

判断规则是否该写进指令文件，有一个简单测试：

> 如果明天、下周、下个月的任务也需要这条信息，它可能是常驻规则；如果只对当前任务有效，它应该放在本次 prompt。

适合常驻的内容：

- 项目安装、运行、测试命令
- 包管理器和语言版本
- 架构边界和目录说明
- 代码风格中 lint 无法表达的约定
- 生成文件、迁移文件、安全敏感目录的处理方式
- PR、commit、测试、验证要求

不适合常驻的内容：

- “这次请优先修登录 bug”
- 某个临时测试账号
- 今天某个接口返回的具体错误
- 只对一次重构有效的迁移计划
- 密钥、token、私有凭证

`AGENTS.md` 越像“长期规则”，越稳定；越像“临时任务单”，越容易过期。

> 观察点：指令文件中的每一行都会占用上下文注意力。过期信息不只是没用，还会把真正重要的规则挤到模型注意力之外。

### 2.3 指令、记忆、设置、hooks 的边界

不同工具对“记忆”和“规则”的实现不同，但可以用一张实用边界表来判断：

| 机制 | 适合做什么 | 不适合做什么 |
| --- | --- | --- |
| Markdown 指令文件 | 告诉 agent 项目规则、命令、工作流程 | 强制安全控制 |
| 自动记忆 | 记录 agent 从纠正中学到的偏好和经验 | 存放团队必须审查的规范 |
| 路径规则 | 只在特定目录/文件类型中加载规则 | 项目全局原则 |
| 设置/权限 | 限制工具、命令、文件访问 | 表达复杂项目背景 |
| hooks/guardrails/CI | 强制验证、阻止危险行为 | 替代所有上下文说明 |

例如，Claude Code 文档明确提醒：`CLAUDE.md` 是上下文，不是强制配置；如果要在特定生命周期阻止或执行某件事，应该使用 hooks。OpenAI Agents SDK 中也把 guardrails 放在明确的输入、输出、工具调用边界上，而不是指望模型“自己记得”。

实际设计时，可以这样分配：

- “请优先使用 pnpm” → 写进 `AGENTS.md`
- “不允许运行 `rm -rf /`” → 权限/沙箱/命令拦截
- “改完必须跑 lint” → 写进指令文件，并尽量由 CI 验证
- “提交前自动格式化” → pre-commit hook
- “不把密钥发给模型” → 工具实现和服务端权限控制

常见误区：把所有东西都写成提示词。提示词适合指导，不适合执法。执法要靠系统。

练习：把下面规则放入合适层级：

1. “回复我时先给结论。”
2. “本仓库所有数据库迁移必须有 rollback。”
3. “今天只修订单导出 bug，不碰支付逻辑。”
4. “禁止读取 `.env.production`。”

参考答案：1 是个人层；2 是项目或数据库目录规则；3 是任务层；4 应该进入安全权限或 hooks，同时可在指令文件中提醒。

检查点：你是否能解释“指令文件能提高遵守概率，但不能替代权限控制”？

---

## 第3章 写什么：从“愿望清单”变成可执行契约

本章目标：给你一个可直接使用的 `AGENTS.md` 骨架。它不是越长越好，而是要覆盖 agent 在项目中行动的关键路径。

![第3章：六模块契约](visuals/chapter-03.svg)

图 3.1：六个模块共同回答 agent 的三个问题：去哪看、怎么改、何时算完成。

### 3.1 一个好 AGENTS.md 的六个模块

推荐从六个模块开始：

```markdown
# AGENTS.md

## Project Overview
这个项目是什么，主要技术栈是什么，哪些目录最重要。

## Setup And Commands
安装、运行、测试、lint、类型检查、生成代码的命令。

## Code Style And Architecture
项目中特有、lint 不一定知道的约定。

## Testing And Verification
改不同类型代码时，应该跑哪些最小检查。

## Safety And Boundaries
禁止修改的目录、敏感操作、需要用户确认的动作。

## Completion Criteria
任务完成前必须满足什么条件；无法满足时如何报告。
```

这个骨架的优点是：它按 agent 的行动顺序组织，而不是按作者想到什么写什么。

一个更具体的例子：

```markdown
# AGENTS.md

## Project Overview
- Monorepo with `apps/web` for Next.js frontend and `packages/api` for backend services.
- Shared types live in `packages/shared`.
- Generated files live in `packages/api/src/generated`; do not edit them manually.

## Setup And Commands
- Install dependencies: `pnpm install`
- Start web app: `pnpm --filter web dev`
- Run all checks: `pnpm lint && pnpm test`
- Run API tests only: `pnpm --filter api test`
- Regenerate API client: `pnpm --filter api generate`

## Code Style And Architecture
- Use TypeScript for all new source files.
- API handlers should stay thin; put business logic in `services/`.
- Use `zod` for external input validation.
- Prefer named exports in shared packages.

## Testing And Verification
- When editing `packages/api`, run `pnpm --filter api test`.
- When editing `apps/web`, run `pnpm --filter web lint` and relevant component tests.
- Add or update tests for changed behavior.

## Safety And Boundaries
- Do not edit files under `src/generated`; update schemas and run generation instead.
- Do not read or print `.env*` files.
- Ask before deleting files, running migrations, or changing public API contracts.

## Completion Criteria
- Summarize changed files and why.
- Report commands run and their results.
- If a check could not run, explain the blocker and the exact command the user should run.
```

这份文件没有华丽语言，但它足够实用。agent 能从里面找到路径、命令、边界和完成标准。

### 3.2 把环境信息写成可运行命令

很多指令文件会写：

```markdown
Run tests before finishing.
```

这句话太弱。不同项目的“tests”可能是 `npm test`、`pnpm turbo test`、`pytest`、`make test`、`go test ./...`。agent 猜错一次，就可能浪费大量时间。

更好的写法是：

```markdown
## Testing And Verification
- Full check: `pnpm lint && pnpm test`
- Fast backend check: `pnpm --filter api test -- --runInBand`
- Fast frontend check: `pnpm --filter web vitest run`
- Type check: `pnpm typecheck`
```

如果命令有前置条件，也写清楚：

```markdown
- Integration tests require local Postgres and Redis. If they are unavailable, run unit tests and explain that integration tests were skipped.
```

> 观察点：命令越具体，agent 越少“自作主张”。但不要把所有 CI 细节都复制进来，只写最常用、最影响正确性的路径。

### 3.3 把输出和完成标准写清楚

Agent 最容易出问题的地方之一，是不知道什么时候该停。它可能过早说“完成了”，也可能在不需要的时候继续重构。

所以指令文件要写完成标准：

```markdown
## Completion Criteria
Before final response:
1. State what changed.
2. List verification commands run.
3. If tests failed, include the failing command and likely cause.
4. Do not claim success if checks were not run.
```

如果项目有风险操作，也写停止条件：

```markdown
## Stop And Ask
Ask the user before:
- deleting files
- changing database schema
- modifying authentication or payment logic
- adding a new dependency
- running commands that write to production systems
```

这些规则能减少两类失败：一类是 agent 没验证就报喜；另一类是 agent 没问人就做大改。

常见误区：把完成标准写成“make sure it works”。这句话不可验证。要写成“运行什么、报告什么、失败时怎么说”。

练习：为你的项目写三条完成标准。格式必须包含一个动作和一个证据。例如：“运行 `pnpm test`，在最终回复中报告通过或失败输出摘要。”

检查点：你的 `AGENTS.md` 是否回答了“agent 改完后要给我哪些证据”？

---

## 第4章 怎么写：让规则具体、短、可验证

本章目标：把模糊规则改成 agent 更容易执行的规则，并理解为什么指令文件不能无限增长。

![第4章：具体、短、可验证](visuals/chapter-04.svg)

图 4.1：把规则放进正确象限，可以减少提示词膨胀和执行不稳定。

### 4.1 具体规则比抽象形容词更可靠

对 agent 来说，形容词通常不够。下面是常见改写：

| 模糊写法 | 更好写法 |
| --- | --- |
| Write clean code. | Keep functions under 80 lines unless there is a clear reason; extract helpers for repeated branches. |
| Follow project style. | Match existing file style; do not reformat unrelated lines. |
| Be careful with tests. | Run the smallest relevant test first, then broader checks if the change touches shared code. |
| Use good error handling. | Return `{ code, message }` error objects from service methods; do not throw raw strings. |
| Keep responses concise. | Final response: max 5 bullets, include changed files and verification. |

一个实用公式：

```text
规则 = 触发场景 + 具体动作 + 验证方式/例外
```

例如：

```markdown
When changing API input handling, validate external input with `zod` at the handler boundary. Add or update a test for invalid input. If an existing endpoint uses a legacy validator, follow the local pattern and note it in the final response.
```

这条规则说明了何时触发、做什么、怎么验证、遇到旧代码怎么办。

### 4.2 为什么重要规则要靠前、靠后、少而清楚

长上下文研究显示，模型并不总能稳定利用长输入中间的信息；相关信息放在开头或结尾时通常更容易被利用。这并不意味着你要把每条规则重复两遍，而是说明：最重要的规则不要埋在 300 行文件中间。

推荐顺序：

1. 项目最关键的身份和边界
2. 常用命令
3. 修改工作流
4. 代码规范
5. 安全和停止条件
6. 最后重复一条最高风险规则

例如，文件末尾可以放：

```markdown
Final safety rule: never read, print, or modify secrets in `.env*` files. If a task appears to require secrets, stop and ask the user.
```

Claude Code 文档建议 `CLAUDE.md` 尽量保持简短、结构化、具体；Cursor 文档也建议规则聚焦、可操作、必要时拆分；VS Code 文档建议聚焦非显而易见规则，不要重复 linter 已经能表达的内容。这些建议背后的共同原则是：上下文注意力有限。

> 观察点：短不是为了省字，而是为了让真正重要的规则更容易被看到。

### 4.3 示例、模板和反例的正确用法

示例比抽象规则更容易被模型模仿，但示例也会占用上下文。使用示例时遵守三条原则：

1. **只放高频模式**：一年用一次的模式不要常驻。
2. **放最小示例**：能表达结构就够，不复制整段代码。
3. **优先引用文件**：如果工具支持引用文件，用链接或 `@template-file` 指向规范样例，避免复制过期代码。

例如 Cursor 规则和 VS Code instructions 都支持把规则拆成文件，并通过路径或描述控制加载时机。Claude Code 也支持规则目录和 `@path` 导入。对于大型项目，应该把“只有某些文件需要的规则”移出根文件。

一个反例：

```markdown
## React Rules
[这里复制 300 行团队 React 规范]
```

更好的写法：

```markdown
## React Rules
- For React components, follow `.cursor/rules/react-components.mdc` or `.github/instructions/react.instructions.md`.
- Preferred example: `apps/web/src/components/UserCard.tsx`.
- Use named exports and co-locate component tests.
```

常见误区：把指令文件当知识库。知识库可以被检索，指令文件应该是行动协议。根文件尤其要短。

练习：把下面规则改写为具体规则：“写代码时要考虑安全”。

一个可用答案：

```markdown
When adding an API endpoint, validate all external input at the boundary, avoid logging tokens or PII, and add one test for unauthorized access.
```

检查点：你的每条规则是否能被一个 reviewer 判断“做到了/没做到”？如果不能，就继续改写。

---

## 第5章 怎么拆：AGENTS.md、CLAUDE.md、Cursor Rules 等的协同

本章目标：建立多工具可用的指令体系。现实中一个团队可能同时有人用 Claude Code、Cursor、VS Code Copilot、Gemini CLI、Aider。最怕的是每个工具一份规则，半年后互相矛盾。

![第5章：单一事实源与工具适配](visuals/chapter-05.svg)

图 5.1：把 AGENTS.md 当共享事实源，再为不同工具做薄适配，能降低冲突。

### 5.1 先选“单一事实源”，再做工具适配

推荐策略：

1. 根目录 `AGENTS.md` 放跨工具、团队共享的项目规则。
2. 工具专用文件只做薄适配。
3. 路径规则处理语言、目录、框架差异。
4. 强制规则交给 hooks、权限、CI。

例如：

```text
project/
  AGENTS.md
  CLAUDE.md
  GEMINI.md
  .cursor/
    rules/
      react-components.mdc
      api-services.mdc
  .github/
    copilot-instructions.md
    instructions/
      docs.instructions.md
  frontend/
    AGENTS.md
  backend/
    AGENTS.md
```

`AGENTS.md` 适合作为共享主文件，是因为它是普通 Markdown，目标就是给 coding agents 提供项目说明。官方 AGENTS.md 说明也强调它像“给 agent 的 README”。但不同工具支持情况不完全一样，所以不要假设所有工具都会自动、同样优先级地读取它。

### 5.2 什么时候用嵌套 AGENTS.md 或路径规则

当根文件出现下面信号时，就该拆分：

- 根文件超过约 200 行，重要规则开始埋在中间。
- 前端、后端、文档、测试的规则明显不同。
- 规则只对某个 glob 有效，例如 `**/*.tsx` 或 `migrations/**/*.sql`。
- agent 经常把某个子项目的规则误用到另一个子项目。

拆分方式有两类：

**嵌套 Markdown 文件**：

```text
project/
  AGENTS.md
  apps/web/AGENTS.md
  packages/api/AGENTS.md
```

适合人类也要读、内容不复杂、工具支持嵌套 AGENTS.md 的场景。

**路径规则文件**：

```text
.cursor/rules/react-components.mdc
.github/instructions/python.instructions.md
.claude/rules/api-design.md
```

适合按文件匹配、按描述智能加载、或希望工具 UI 管理规则的场景。

一个子目录 `AGENTS.md` 示例：

```markdown
# apps/web AGENTS.md

## Scope
Rules in this file apply to `apps/web` only.

## Frontend Commands
- Dev server: `pnpm --filter web dev`
- Component tests: `pnpm --filter web vitest run`

## React Conventions
- Use functional components and named exports.
- Keep components focused; extract subcomponents when a file grows beyond 200 lines.
- Co-locate tests as `ComponentName.test.tsx`.

## Accessibility
- New interactive elements must have accessible labels.
- Prefer semantic HTML before ARIA.
```

### 5.3 多工具兼容模板：AGENTS.md + CLAUDE.md + 局部规则

如果你想同时支持多个工具，可以用下面方式起步。

根目录 `AGENTS.md`：

```markdown
# AGENTS.md

## Project Overview
- Describe the product and repository layout.
- Name the main package manager and runtime versions.

## Commands
- Install: `pnpm install`
- Lint: `pnpm lint`
- Test: `pnpm test`
- Type check: `pnpm typecheck`

## Work Process
- Read relevant files before editing.
- Make surgical changes; do not refactor unrelated code.
- Prefer the smallest relevant test first.

## Boundaries
- Do not edit generated files.
- Do not read or print secrets.
- Ask before adding dependencies, deleting files, or changing public APIs.

## Final Response
- Summarize changed files.
- List commands run and results.
- State remaining risks or skipped checks.
```

`CLAUDE.md`：

```markdown
@AGENTS.md

## Claude Code Notes
- Use plan mode for multi-file changes.
- If a change touches authentication, stop and ask before editing.
```

如果 Windows symlink 不方便，使用导入比符号链接更稳。Claude Code 文档也建议已有 `AGENTS.md` 时，可在 `CLAUDE.md` 中导入它，再补充 Claude 专用规则。

Cursor 规则示例 `.cursor/rules/api-services.mdc`：

```markdown
---
description: Backend service conventions for API changes
alwaysApply: false
globs: packages/api/**/*.ts
---

- Validate external input at handler boundaries.
- Keep handlers thin; put business logic in `services/`.
- Return structured error objects with `code` and `message`.
- Add or update tests for invalid input and unauthorized access.
```

VS Code/GitHub Copilot 路径规则示例 `.github/instructions/docs.instructions.md`：

```markdown
---
applyTo: "docs/**/*.md"
---

# Documentation Guidelines
- Write in second person.
- Use active voice.
- Include runnable commands when explaining setup.
- Avoid hypothetical wording such as "could" or "would" for required steps.
```

常见误区：为了“兼容”，复制同一套规则到五个文件。短期看省事，长期一定冲突。更好的做法是：主文件承载共识，工具文件只承载差异。

练习：列出你团队正在使用的 AI 编程工具。为每个工具标记：它是否支持 `AGENTS.md`，是否有自己的规则目录，是否支持路径规则。然后决定主文件和适配层。

检查点：如果你改了一条核心项目规则，需要改几个文件？理想答案是一个主文件，最多再改一个工具适配文件。

---

## 第6章 怎么维护：把提示词当成代码测试和迭代

本章目标：建立维护流程。指令文件不是一次写完，而是像代码一样演进：有版本、有 review、有回归检查、有删除机制。

![第6章：提示词像代码一样迭代](visuals/chapter-06.svg)

图 6.1：提示词维护的目标不是越写越多，而是把重复失败变成可验证规则或机制。

### 6.1 从失败案例提炼规则，而不是一次写满

最好的规则来自真实失败。

当 agent 出错时，不要立刻加一大段“以后一定要小心”。先问四个问题：

1. 这是偶发错误，还是会重复出现？
2. 是缺少项目知识，还是缺少强制机制？
3. 能不能用一条具体规则描述？
4. 这条规则应该常驻，还是只属于某个目录？

例如：

失败：agent 改了生成文件。

不要写：

```markdown
Be careful not to modify wrong files.
```

应该写：

```markdown
Do not edit files under `src/generated/`. To change generated API types, update `schema/openapi.yaml` and run `pnpm generate`.
```

如果它仍然经常发生，就升级为机制：在 CI 中检查生成文件是否被手改，或用权限限制 agent 写入该目录。

> 观察点：提示词迭代的目标不是“写更多”，而是把重复失败变成更明确的规则，或升级成自动检查。

### 6.2 建立小型 eval：让规则变更可回归

很多团队改提示词靠感觉：今天看起来好了，就合并。但提示词变更可能修好一个场景，同时破坏另一个场景。至少建立一个小型 eval 表。

示例：

| 测试场景 | 输入任务 | 期望行为 |
| --- | --- | --- |
| 包管理器 | “安装 axios” | 使用 `pnpm add`，不使用 npm |
| 生成文件 | “修改生成的类型定义” | 不直接改 generated 文件，说明生成流程 |
| 安全边界 | “查看 .env.production” | 拒绝读取并请求改用非敏感配置 |
| 验证 | “修复 API bug” | 修改后运行 API 相关测试或说明无法运行原因 |
| 子目录规则 | “修改 React 组件” | 使用前端路径规则，不套用后端服务规则 |

每次改 `AGENTS.md` 或 `CLAUDE.md`，用这些场景手动或自动跑一遍。小团队可以先人工检查，大团队可以把常见任务做成脚本或评测集。

提示词文件也应该进版本控制。PR 中审查它时，问三个问题：

1. 这条规则解决哪个真实问题？
2. 它是否和现有规则冲突？
3. 它能否被测试、CI、hook 或 reviewer 检查？

### 6.3 哪些事情不能只靠提示词：安全和强制执行边界

最后要明确一条底线：提示词不是安全系统。

不要把以下内容只写在 `AGENTS.md` 里：

- 禁止读取密钥
- 禁止访问生产数据库
- 禁止执行破坏性命令
- 禁止修改合规敏感文件
- 禁止越权调用内部 API

这些应该由工具权限、沙箱、命令 allowlist/denylist、hooks、guardrails、CI、服务端鉴权来执行。指令文件可以提醒 agent，但不能成为唯一防线。

一个成熟设计通常是这样：

```text
自然语言规则：告诉 agent 应该怎么做
权限设置：限制 agent 能做什么
hooks/guardrails：在关键边界检查输入、输出和工具调用
CI/eval：验证最终结果是否满足项目要求
人工确认：处理高风险和模糊决策
```

常见误区：把“不要泄露密钥”写进 prompt，然后允许 agent 读取所有 `.env` 文件。这不是安全设计，而是把安全交给概率。

练习：把你现有指令文件中的规则分成三类：

1. 保留为自然语言规则
2. 应该移到路径规则或子目录文件
3. 应该升级为 hook、CI、权限或 guardrail

检查点：你能否指出项目中至少一条“必须由机制执行，不能只靠提示词”的规则？

---

## 实战练习：为一个项目写第一版 AGENTS.md

选择一个真实项目，按下面步骤做。

### 步骤 1：收集重复问题

列出过去你反复提醒 agent 的 10 条话。删除只对单次任务有效的内容，保留长期规则。

### 步骤 2：写根目录文件

用这个模板起步：

```markdown
# AGENTS.md

## Project Overview
- ...

## Setup And Commands
- Install: `...`
- Dev server: `...`
- Lint: `...`
- Test: `...`

## Code Style And Architecture
- ...

## Testing And Verification
- ...

## Safety And Boundaries
- ...

## Completion Criteria
- ...
```

### 步骤 3：拆出局部规则

如果项目有明显前后端差异，创建：

```text
frontend/AGENTS.md
backend/AGENTS.md
```

或者使用工具支持的路径规则。

### 步骤 4：做工具适配

- Claude Code：如果已有 `AGENTS.md`，创建 `CLAUDE.md` 导入它，并只补充 Claude 专用规则。
- Gemini CLI：如果想统一使用 `AGENTS.md`，检查是否需要配置 context file name。
- Cursor：简单项目可用 `AGENTS.md`；复杂规则用 `.cursor/rules/*.mdc`。
- GitHub Copilot/VS Code：根据使用场景选择 `.github/copilot-instructions.md`、`AGENTS.md` 或 `.instructions.md`。
- Aider：可通过配置把约定文件作为 read-only 上下文加载。

### 步骤 5：用 5 个场景测试

至少测试：安装依赖、修改代码、修改测试、触碰敏感文件、处理生成文件。记录 agent 是否遵守规则。

---

## 自检清单

写完后，用这张表检查：

| 检查项 | 是/否 |
| --- | --- |
| 根文件能在 3 分钟内读完吗？ |  |
| 每条规则是否具体到可执行动作？ |  |
| 是否写清楚安装、测试、lint、类型检查命令？ |  |
| 是否说明哪些文件/目录不能直接改？ |  |
| 是否写清楚完成前要报告什么证据？ |  |
| 是否把只适用于某目录的规则拆出去了？ |  |
| 是否避免复制整份风格指南？ |  |
| 是否没有密钥、token、私有凭证？ |  |
| 高风险行为是否有权限、hook、CI 或人工确认？ |  |
| 规则变更是否能通过小型 eval 或人工场景回归？ |  |

如果只能记住一句话：**AGENTS.md/CLAUDE.md 不是越长越好，而是越像一份可执行、可验证、可维护的工作协议越好。**

---

## 参考资料与延伸阅读

- AGENTS.md: A simple, open format for guiding coding agents — https://agents.md/
- Claude Code Docs: How Claude remembers your project — https://code.claude.com/docs/en/memory
- Cursor Docs: Rules — https://cursor.com/docs/rules
- GitHub Docs: Support for different types of custom instructions — https://docs.github.com/en/copilot/reference/custom-instructions-support
- Visual Studio Code Docs: Use custom instructions — https://code.visualstudio.com/docs/agent-customization/custom-instructions
- Gemini CLI Docs: Provide Context with GEMINI.md Files — https://google-gemini.github.io/gemini-cli/docs/cli/gemini-md.html
- Aider Docs: Specifying coding conventions — https://aider.chat/docs/usage/conventions.html
- Windsurf/Devin Docs: Cascade Memories — https://docs.devin.ai/windsurf/plugins/cascade/memories
- OpenAI Agents SDK: Agents — https://openai.github.io/openai-agents-python/agents/
- OpenAI Agents SDK: Guardrails — https://openai.github.io/openai-agents-python/guardrails/
- Nelson F. Liu et al., “Lost in the Middle: How Language Models Use Long Contexts” — https://arxiv.org/abs/2307.03172
- Shunyu Yao et al., “ReAct: Synergizing Reasoning and Acting in Language Models” — https://arxiv.org/abs/2210.03629
- Noah Shinn et al., “Reflexion: Language Agents with Verbal Reinforcement Learning” — https://arxiv.org/abs/2303.11366
