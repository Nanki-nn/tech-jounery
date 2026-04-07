# 🔗 LangChain 介绍

## 🤔 什么是 LangChain？

LangChain 是一个用于构建 **LLM 应用和 Agent** 的开源框架 🚀。它的目标不是自己提供模型，而是把**模型、工具、检索、工作流和外部系统**连接起来，让开发者更快搭建 AI 应用。

如果用一句话概括：

```
LangChain = 把大模型接入业务系统的“应用层框架”
```

它特别适合下面这些场景：

- 🤖 Agent 应用
- 📚 RAG / 知识库问答
- 🔌 工具调用（Tool Calling）
- 🌐 多模型、多服务集成
- 🧠 需要把 Prompt、模型、检索、工具串起来的 AI 系统

根据 LangChain 当前官方文档的定位，LangChain 更强调“**快速构建 Agent 和应用**”，而底层运行时能力则由 LangGraph 提供。

---

## 😮 为什么 LangChain 会流行？

直接调用模型 API 并不难，但一旦应用进入真实业务，问题会立刻变复杂：

```
用户输入
   ↓
Prompt 组装
   ↓
模型调用
   ↓
工具调用 / 检索
   ↓
上下文管理
   ↓
结果格式化
   ↓
日志、调试、评估
```

如果每个环节都自己拼，工程成本会迅速上升。LangChain 的价值就在于把这些常见能力标准化。

| 能力 | 说明 |
|------|------|
| 🔌 统一模型接口 | 屏蔽不同模型厂商 API 差异 |
| 🧰 工具调用 | 让模型使用函数、API、数据库、搜索能力 |
| 📚 检索增强 | 更方便接入 RAG 流程 |
| 🧠 Agent 抽象 | 让模型在多步推理中决定何时调用工具 |
| 🔍 可观测性配套 | 可结合 LangSmith 做调试和评估 |

---

## 🏗️ LangChain 的核心思路

LangChain 本身不是“大模型”，而是一组可组合的构件。

一个典型的 LangChain 应用，可以抽象成下面这条链路：

```
用户问题
   ↓
🧠 Model
   ↓
🧰 Tools / Retrieval
   ↓
🧱 Orchestration
   ↓
📤 Final Response
```

它的关键思想不是“帮你写 Prompt”这么简单，而是：

- 把模型调用标准化
- 把外部工具接入统一化
- 把应用流程组合化
- 把复杂 AI 工作流工程化

---

## 🧩 LangChain 的核心组件

### 1. 🧠 Model

Model 是应用的推理核心。LangChain 提供统一接口来接入 OpenAI、Anthropic、Google 等不同模型供应商。

例如：

```python
from langchain_openai import ChatOpenAI

model = ChatOpenAI(model="gpt-4.1-mini")
response = model.invoke("用一句话介绍 LangChain")
print(response.content)
```

### 2. 🧰 Tool

Tool 是让模型能够“做事”的方式，例如查天气、调用数据库、查询 API、搜索网页。

```python
from langchain.tools import tool

@tool
def get_weather(city: str) -> str:
    """Get the weather for a city."""
    return f"{city} is sunny."
```

### 3. 🤖 Agent

Agent 会让模型自己判断“何时调用工具、调用哪个工具、是否继续下一步”。

这也是 LangChain 当前最核心的能力之一。

### 4. 📚 Retrieval

Retrieval 负责把外部知识在运行时取回来，是构建 RAG 的关键基础。

### 5. 🧱 Middleware / Runtime

当应用变复杂后，开发者会关心：

- 模型路由
- 重试
- 状态管理
- 人工介入
- 持久化

这些更底层的编排能力，当前官方体系里更多由 LangGraph 承担。

---

## 🧭 LangChain、LangGraph、LangSmith 是什么关系？

这是很多人第一次接触时最容易混淆的地方。

| 组件 | 定位 |
|------|------|
| `LangChain` | 高层应用框架，偏模型集成与 Agent 开发 |
| `LangGraph` | 更底层的编排和运行时框架，偏状态化 Agent 工作流 |
| `LangSmith` | 调试、追踪、评估、观测平台 |

可以简单理解为：

```
LangChain 负责“好上手”
LangGraph 负责“可控制”
LangSmith 负责“看得见”
```

如果你只是想快速做一个 Agent，通常从 LangChain 开始就够了。  
如果你要做复杂的多步骤、可恢复、可持久化的 Agent 工作流，再考虑深入到 LangGraph。

---

## 📦 LangChain 的安装方式

当前 LangChain Python 版本推荐单独安装核心包和 provider 集成包。

### 安装核心包

```bash
pip install -U langchain
```

### 安装 OpenAI 集成

```bash
pip install -U langchain-openai
```

LangChain 当前官方文档要求：

```bash
Python 3.10+
```

这也是近几个版本比较明确的要求。

---

## 🚀 一个最小可运行示例

如果只想快速理解 LangChain 的基本使用方式，可以先看一个最小模型调用示例：

```python
from langchain_openai import ChatOpenAI

model = ChatOpenAI(model="gpt-4.1-mini")

response = model.invoke("请用一句话解释什么是 RAG")
print(response.content)
```

这个例子说明了 LangChain 的第一层价值：

```
统一模型接口
```

你不需要直接手写底层 HTTP 请求，就可以以统一方式调用模型。

---

## 🧪 Agent 示例

根据 LangChain 当前官方 Python 文档，`create_agent` 是创建 Agent 的标准入口之一。

```python
from langchain.agents import create_agent
from langchain.tools import tool

@tool
def get_weather(city: str) -> str:
    """Get weather for a given city."""
    return f"{city} is sunny."

agent = create_agent(
    model="openai:gpt-4.1-mini",
    tools=[get_weather],
    system_prompt="You are a helpful assistant"
)

result = agent.invoke(
    {"messages": [{"role": "user", "content": "北京天气怎么样？"}]}
)

print(result["messages"][-1].content)
```

这个例子体现了 LangChain 现在最重要的能力：

- 模型标准化接入
- 工具注册
- Agent 决策循环
- 统一消息调用接口

---

## 📚 LangChain 为什么很适合做 RAG？

LangChain 适合做 RAG，不是因为它“比别人更聪明”，而是因为它把 RAG 所需的基础能力都做了统一封装：

| 组件 | 作用 |
|------|------|
| Loader | 加载外部文档 |
| Splitter | 文本切分 |
| Embedding | 文本向量化 |
| Vector Store | 存储向量 |
| Retriever | 检索相关片段 |
| Model | 生成最终回答 |

也就是说，LangChain 本质上是 RAG 的一个“总装框架”。

---

## ⚔️ LangChain 的优势与注意点

### 优势

| 优势 | 说明 |
|------|------|
| 🔌 集成广 | 主流模型、向量库、工具生态很多 |
| 🚀 原型快 | 很适合快速验证 AI 应用 |
| 🧠 Agent 能力强 | 当前官方重点投入方向之一 |
| 🔄 可组合 | 适合逐步从简单应用演进为复杂系统 |

### 注意点

| 注意点 | 说明 |
|--------|------|
| 🧱 抽象较多 | 初学时容易感觉概念很多 |
| 🔁 版本变化快 | 旧教程和新版本 API 可能不一致 |
| 🧭 容易混淆 | LangChain、LangGraph、LangSmith 经常被混为一谈 |

所以学习 LangChain 时，一个很重要的原则是：

```
优先看当前官方文档，不要盲目照抄旧博客
```

---

## 🆚 LangChain vs LlamaIndex

很多人会把 LangChain 和 LlamaIndex 放在一起比较。

| 维度 | LangChain | LlamaIndex |
|------|-----------|------------|
| 🎯 核心定位 | Agent / 应用编排 | 知识接入 / 检索 |
| 🤖 Agent 支持 | 强 | 一般 |
| 📚 文档问答体验 | 好 | 很好 |
| 🔌 生态集成 | 很强 | 强 |
| 🏗️ 适合场景 | 通用 LLM 应用 | 知识库问答、文档检索 |

简单理解：

```
LangChain 更像“AI 应用总装平台”
LlamaIndex 更像“知识库接入专家”
```

---

## 🌟 典型应用场景

- 🤖 智能 Agent：调用搜索、数据库、业务接口完成任务
- 📚 企业知识库问答：结合检索和模型生成答案
- 💬 智能客服：接入 FAQ、工单系统和产品资料
- 🧑‍💻 AI Copilot：连接代码库、文档和工具链
- 🔌 多工具工作流：模型根据任务动态调用不同工具

---

## 🗺️ 架构总结

```
┌─────────────────────────────────────────────┐
│              🔗 LangChain 应用架构           │
│                                             │
│  ┌──────────┐    ┌──────────────────────┐  │
│  │ 💬 用户输入 │───▶│   🧠 LangChain 应用层    │  │
│  └──────────┘    │  • Model             │  │
│                  │  • Tools             │  │
│                  │  • Agent             │  │
│                  │  • Retrieval         │  │
│                  └──────────┬───────────┘  │
│                             │               │
│                    ┌────────▼────────┐      │
│                    │ 🧱 LangGraph 运行时│     │
│                    │  • State         │      │
│                    │  • Orchestration │      │
│                    │  • Persistence   │      │
│                    └────────┬────────┘      │
│                             │               │
│                    ┌────────▼────────┐      │
│                    │ 🔌 模型 / 工具 / DB │     │
│                    │  / 向量库 / API    │     │
│                    └────────┬────────┘      │
│                             │               │
│                    ┌────────▼────────┐      │
│                    │ 🔍 LangSmith     │      │
│                    │  • Tracing       │      │
│                    │  • Eval          │      │
│                    └─────────────────┘      │
└─────────────────────────────────────────────┘
```

---

## 📌 总结

LangChain 的真正价值，不是“替你做 AI”，而是让你能更快地把模型能力接到真实业务里。

它适合的不是单纯聊天，而是：

- 让模型接工具
- 让模型接知识
- 让模型接业务系统
- 让应用从 Demo 逐步演进到工程化系统

如果你想快速做一个 AI Agent 或 RAG 应用，LangChain 依然是非常值得优先考虑的框架。

---

## 📚 参考资源

- 📖 官方总览：[docs.langchain.com/oss/python/langchain/overview](https://docs.langchain.com/oss/python/langchain/overview)
- 📦 安装文档：[docs.langchain.com/oss/python/langchain/install](https://docs.langchain.com/oss/python/langchain/install)
- 🤖 Agent 文档：[docs.langchain.com/oss/python/langchain/agents](https://docs.langchain.com/oss/python/langchain/agents)
- 📚 Retrieval 文档：[docs.langchain.com/oss/python/langchain/retrieval](https://docs.langchain.com/oss/python/langchain/retrieval)
- 💬 OpenAI 集成：[docs.langchain.com/oss/python/integrations/chat/openai](https://docs.langchain.com/oss/python/integrations/chat/openai)
- 🧱 LangGraph 总览：[docs.langchain.com/oss/python/langgraph/overview](https://docs.langchain.com/oss/python/langgraph/overview)
