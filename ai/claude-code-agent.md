# 🧠 Claude Code 的 Agent 系统怎么分层？

## 🤔 先说结论

如果只看表面，Claude Code 很像“一个会写代码的大模型 + 一个终端” 🤖。  
但从 Anthropic 公开的 **tools、subagents、memory、hooks、permissions** 这些能力来看，它更像一套分层的 Agent 系统，而不是单一模型直接操作环境。

可以先把它抽象成下面这张图：

```text
💬 用户请求
   ↓
🗺️ Planner：理解目标、拆任务、决定下一步
   ↓
🧰 Tool Layer：读文件、搜代码、改代码、跑命令、调子代理
   ↓
🧠 Memory Layer：项目记忆、用户偏好、会话压缩、当前任务状态
   ↓
✅ Policy / Hooks：权限控制、前后置校验、风险约束
   ↓
📤 最终响应
```

> ⚠️ 说明：Anthropic 没有公开 Claude Code 的完整内部系统 prompt 和全部运行时实现。下面的内容是基于官方公开文档能力做的工程化拆解，重点在“**如何理解这类 Agent 系统的分层**”，而不是声称拿到了官方内部架构图。

---

## 😮 为什么 Claude Code 不能只理解成“一个模型”？

真实的 Coding Agent 不只是回答问题，它还要连续完成一串动作：

```text
理解需求
   ↓
分析代码库
   ↓
拆分任务
   ↓
调用工具
   ↓
修改文件
   ↓
运行测试
   ↓
根据结果继续修复
   ↓
输出总结
```

如果把这些事情都塞给同一个 Prompt，通常会遇到几个问题：

| 问题 | 说明 |
|------|------|
| 🧠 上下文膨胀 | 读文件、命令输出、计划、结果全堆在一起，很快变乱 |
| 🔁 多步任务不稳定 | 模型容易忘记自己前面做到哪一步 |
| 🧰 工具调用失控 | 没有边界时，工具使用会变得不稳定 |
| 🛡️ 安全性不足 | 写文件、执行 Bash、访问 Web 都需要约束 |
| 👥 协作效率低 | 没法把子问题交给单独上下文去处理 |

所以 Claude Code 这类系统真正重要的，不只是模型能力，而是：

- 🗺️ Planner 怎么拆任务
- 🧰 Tool 怎么调用和管控
- 🧠 Memory 怎么保存上下文
- 🛡️ Permissions / Hooks 怎么守住边界

---

## 🏗️ 从公开能力看，Claude Code 可以怎么拆？

根据官方文档，Claude Code 公开暴露了几类关键能力：

- 🧰 内置工具：`Read`、`Grep`、`Glob`、`LS`、`Edit`、`Write`、`MultiEdit`、`Bash`、`Task`、`TodoWrite`、`WebSearch`、`WebFetch`
- 🤖 子代理：`subagents`，并且每个子代理有**独立上下文窗口**和**自己的工具权限**
- 🧠 记忆系统：`CLAUDE.md` 分层记忆，支持用户级、项目级、企业级
- 🔧 系统控制：`settings.json`、`permissions`、`hooks`
- 💬 会话控制：`/memory`、`/agents`、`/permissions`、`/compact` 等命令

从工程角度，最合理的拆法通常是下面这样：

| 层 | 主要职责 | Claude Code 公开能力里对应的线索 |
|------|------|------|
| 🗺️ Planner Layer | 理解需求、拆任务、重规划、决定是否派子代理 | `TodoWrite`、`Task`、subagents |
| 🧰 Tool Layer | 观察环境、执行修改、验证结果 | `Read`/`Grep`/`Edit`/`Write`/`Bash` 等 |
| 🧠 Memory Layer | 保存长期约束、项目规则、用户偏好、会话摘要 | `CLAUDE.md`、`/memory`、`/compact` |
| 🛡️ Policy Layer | 约束工具权限、执行前后检查、限制危险操作 | `permissions`、`hooks` |
| 💬 Interaction Layer | 与用户交互、展示过程、接受人工干预 | `/agents`、`/permissions`、`/review` 等 |

其中最核心的三层，就是 Planner、Tool、Memory。

---

## 🗺️ Planner 层：负责“想清楚下一步做什么”

Claude Code 官方文档没有直接把内部模块命名为 “Planner”，但从它的工作方式看，**Planner 是最合理的工程抽象**。

因为一个 Coding Agent 至少要完成三件事：

### 1. 🎯 目标理解

用户说：

```text
帮我修登录 bug，并补上测试
```

系统不能直接把这句话当成一个动作，它必须先理解成若干阶段：

- 找到登录逻辑在哪
- 分析 bug 原因
- 设计修复方案
- 修改代码
- 运行测试
- 根据测试失败继续修

这就是 Planner 的第一层职责：把自然语言目标，转成任务目标树。

### 2. 📝 任务拆解

Claude Code 公开提供了 `TodoWrite` 工具，这本身就是一个强信号：  
这类系统不是纯“边想边做”，而是会把任务外化为结构化待办列表。

一个典型的 Planner 输出，更像这样：

```text
1. 定位认证相关模块
2. 复现登录失败路径
3. 修复 token 校验逻辑
4. 补充单元测试
5. 运行测试验证
```

也就是说，Planner 的价值不是“想得很玄”，而是：

- 把复杂目标拆成可执行步骤
- 保持当前进度可追踪
- 在失败后能重新规划

### 3. 🤖 子任务委派

Claude Code 官方支持 `Task` 工具和自定义 `subagents`。  
文档明确提到：子代理可以有**单独的 system prompt、单独的工具权限、单独的上下文窗口**。

这说明 Planner 还承担了一个重要职责：

```text
判断哪些问题适合主代理自己做
判断哪些问题适合交给子代理并行或隔离处理
```

例如：

- 🔍 让一个子代理专门做代码搜索
- 🧪 让一个子代理专门跑测试并汇总失败信息
- 🛡️ 让一个子代理专门做 code review

### 4. 🔄 动态重规划

一个合格的 Planner 不是“一次性计划器”，而是循环工作的：

```text
制定计划
   ↓
执行一步
   ↓
读取结果
   ↓
更新 Todo
   ↓
决定下一步
```

所以从工程上看，Planner 最少应该拆成三块：

| 子模块 | 作用 |
|------|------|
| 🎯 Goal Planner | 把用户目标翻译成阶段性目标 |
| 📝 Step Planner | 把阶段目标拆成具体动作 |
| 🔄 Replanner | 根据工具结果调整后续步骤 |

---

## 🧰 Tool 层：负责“真的去做事”

Claude Code 的 Tool 层是公开能力里最清晰的一层，因为官方文档直接列出了可用工具。

这些工具并不是简单的“插件列表”，它们更像一套受控执行系统。

### 1. 👀 观察类工具

这类工具主要用于理解环境，副作用较小：

- `Read`
- `Grep`
- `Glob`
- `LS`
- `NotebookRead`
- `WebSearch`
- `WebFetch`

它们解决的是：

```text
代码在哪里？
谁调用了谁？
这个文件里写了什么？
相关文档是什么？
```

对 Planner 来说，这一层是“感知世界”的入口。

### 2. ✍️ 执行类工具

这类工具会真正修改环境：

- `Edit`
- `Write`
- `MultiEdit`
- `NotebookEdit`
- `Bash`

它们解决的是：

```text
改代码
新建文件
运行测试
执行构建
格式化代码
```

这也是最需要权限控制的一层。

### 3. 🧭 调度类工具

这类工具更接近 Agent 内部编排：

- `TodoWrite`：把任务状态结构化
- `Task`：把子问题交给子代理

也就是说，Claude Code 的工具不只是“对外部世界做操作”，还包括“对 Agent 自身执行流程做操作”。

### 4. ✅ 验证类动作

官方工具列表里没有单独叫 “Verify” 的工具，但在 Coding Agent 里，验证通常通过 `Bash` 完成，例如：

- `npm test`
- `pytest`
- `cargo test`
- `npm run build`
- `ruff check`
- `tsc --noEmit`

所以从架构上更合理的理解是：

| 工具层子类 | 典型工具 | 目标 |
|------|------|------|
| 👀 Observe | `Read` `Grep` `LS` | 获取上下文 |
| ✍️ Act | `Edit` `Write` `Bash` | 执行动作 |
| 🧭 Orchestrate | `TodoWrite` `Task` | 管理流程 |
| ✅ Verify | 通常由 `Bash` 触发测试/构建 | 确认结果 |

---

## 🛡️ Tool 层外面，还需要一层 Policy

如果只有工具，没有约束，Agent 的稳定性和安全性都会很差。

Claude Code 文档公开了两类很关键的护栏：

### 1. 🔐 Permissions

`settings.json` 里可以配置：

- `allow`
- `ask`
- `deny`
- `additionalDirectories`
- `defaultMode`

这意味着 Tool 层并不是“模型想调什么就调什么”，而是受策略系统控制。

例如：

```json
{
  "permissions": {
    "allow": ["Bash(npm run test:*)"],
    "deny": ["Read(./.env)", "Bash(curl:*)"]
  }
}
```

这类设计很重要，因为 Coding Agent 最大的风险，往往不在“答错”，而在“做错动作”。

### 2. 🪝 Hooks

Claude Code 还支持 `PreToolUse` 和 `PostToolUse` hooks。

这说明 Tool 调用前后，系统还可以插入额外控制逻辑，比如：

- 在写文件前做校验
- 在写文件后自动跑 formatter
- 在执行危险 Bash 前阻断
- 在子代理完成后记录状态

这类机制说明：  
一个成熟的 Agent 系统里，**Tool 不是裸调用，而是经过策略层包裹的调用**。

---

## 🧠 Memory 层：负责“让 Agent 不会每一步都失忆”

Claude Code 官方文档里，Memory 是公开得比较明确的一层。

它至少包含两类能力：

### 1. 📚 显式长期记忆：`CLAUDE.md`

官方文档明确提到 Claude Code 支持多层记忆文件：

| 记忆类型 | 位置 | 作用 |
|------|------|------|
| 🏢 Enterprise Memory | 系统级 `CLAUDE.md` | 组织统一规范 |
| 📁 Project Memory | `./CLAUDE.md` | 项目架构、团队约定、常用命令 |
| 👤 User Memory | `~/.claude/CLAUDE.md` | 个人偏好、个人工作流 |

而且 Claude Code 会：

- 从当前目录向上递归查找 `CLAUDE.md`
- 在访问某些子目录时，按需加载子树中的记忆文件

这类设计非常像：

```text
组织规则
   ↓
项目规则
   ↓
个人偏好
```

也就是说，Claude Code 的长期记忆不是“把所有对话存下来”，而是把**稳定约束**写成结构化文本配置。

### 2. 💬 会话记忆：当前上下文与压缩

Claude Code 公开提供 `/compact`，这说明会话上下文不是无限增长的，而是需要压缩和总结。

从工程角度，这一层通常可以理解成：

- 当前任务目标
- 最近读过的关键文件
- 最新一次测试结果
- 当前未完成 Todo
- 本轮会话的压缩摘要

这类记忆更接近“工作记忆”，不是永久规则，但对当前任务非常重要。

### 3. 🤖 子代理的隔离记忆

官方文档明确说 subagent 有**独立上下文窗口**。  
这意味着 Claude Code 在 Memory 设计上，不是一个大上下文池，而是更接近：

```text
主代理上下文
   ├── 子代理 A 上下文
   ├── 子代理 B 上下文
   └── 子代理 C 上下文
```

这样做的好处非常直接：

- 🧼 避免不同子任务互相污染
- 📏 控制上下文长度
- 🎯 让某个子代理只关注一个明确问题

---

## 🧱 从工程视角，Memory 最好再分成三层

结合 Claude Code 的公开能力，如果你自己要做类似系统，最实用的 Memory 分层通常是这样：

| 层 | 主要内容 | 对应 Claude Code 线索 |
|------|------|------|
| 🪨 Stable Memory | 组织规范、项目说明、个人偏好 | `CLAUDE.md` 分层记忆 |
| 💬 Session Memory | 当前会话摘要、最近结论、当前 Todo | `/compact`、会话上下文 |
| 🧠 Working Memory | 当前这一步最需要的事实和观察结果 | 运行时上下文、Todo、工具结果 |

可以把它理解成：

```text
Stable Memory = 长期不变的规则
Session Memory = 本轮任务阶段性结论
Working Memory = 当前一步必须盯着看的信息
```

如果不做这个区分，Agent 很容易出现两种问题：

- 什么都记，结果上下文越来越脏
- 什么都不记，每一步都重新探索代码库

---

## 🔄 Planner、Tool、Memory 三层是怎么闭环的？

一个典型的 Claude Code 式工作流，大致可以抽象成这样：

```text
💬 用户提出需求
   ↓
🗺️ Planner 生成 Todo
   ↓
🧰 Tool 读取代码 / 搜索调用链 / 运行命令
   ↓
🧠 Memory 记录关键发现与当前状态
   ↓
🗺️ Planner 根据新信息重规划
   ↓
🤖 必要时调用 Task / Subagent
   ↓
✍️ 修改代码并通过 Bash 验证
   ↓
🪝 Hooks / Permissions 约束执行边界
   ↓
📤 输出结果
```

这个闭环里，三层各自负责的事情非常清楚：

- 🗺️ Planner 决定方向
- 🧰 Tool 接触现实
- 🧠 Memory 保持连续性

如果少了其中任何一层，系统都会明显退化：

| 缺哪层 | 会发生什么 |
|------|------|
| 少 Planner | 只能零散地调用工具，无法稳定完成长任务 |
| 少 Tool | 只能“说会做”，不能真的操作环境 |
| 少 Memory | 每一步都像重新开机，容易重复劳动 |

---

## 💡 如果自己做一个类似 Claude Code 的 Agent，建议怎么拆？

从工程实践看，比较稳的方式通常是：

### 1. 🗺️ Planner 不直接改文件

Planner 负责：

- 理解目标
- 拆任务
- 安排顺序
- 决定是否调用子代理

真正的文件修改和命令执行，交给 Tool 层。

### 2. 🧰 Tool 一定要分“读”和“写”

至少应该分开：

- 👀 Read-only：搜索、读取、列目录
- ✍️ Mutating：写文件、执行命令
- ✅ Verify：测试、构建、Lint

这样更容易做权限控制和失败恢复。

### 3. 🧠 Memory 一定要区分“规则”和“状态”

不要把以下内容混在一起：

- 项目规范
- 用户偏好
- 当前会话摘要
- 当前步骤观察结果

长期规则应该稳定，运行时状态应该可压缩、可替换、可过期。

### 4. 🤖 子代理必须有独立上下文

如果子代理和主代理共用一个巨大上下文，长期来看通常会越来越乱。  
独立上下文窗口是 Claude Code 公开文档里非常关键的设计信号。

### 5. 🛡️ 最外层必须有 Policy

真正让 Agent 可落地的，不只是“会不会调工具”，而是：

- 能不能限制它读哪些文件
- 能不能要求它对某些命令先确认
- 能不能在写完代码后自动做校验

这也是 `permissions` 和 `hooks` 这类能力重要的原因。

---

## 🧭 一句话总结

如果用一句话概括 Claude Code 这类 Agent 系统：

```text
Planner 决定下一步做什么
Tool 负责真正去做
Memory 负责让系统跨步骤保持连续
Policy 负责让这一切可控
```

所以，Claude Code 的核心不是“一个很强的模型”，而是把 **规划、执行、记忆、约束** 这几件事拆开了。

也正因为这几个层次拆得清楚，它才更像一个可持续工作的 Coding Agent，而不是只会在对话框里回答问题的聊天机器人 ✅。

---

## 📚 参考资源

- [Claude Code Overview](https://docs.anthropic.com/en/docs/claude-code/overview)
- [Claude Code Settings](https://docs.anthropic.com/en/docs/claude-code/settings)
- [Manage Claude's Memory](https://docs.anthropic.com/en/docs/claude-code/memory)
- [Subagents](https://docs.anthropic.com/en/docs/claude-code/sub-agents)
- [Hooks Reference](https://docs.anthropic.com/en/docs/claude-code/hooks)
- [Slash Commands](https://docs.anthropic.com/en/docs/claude-code/slash-commands)
