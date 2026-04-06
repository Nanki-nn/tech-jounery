# 📚 RAG 库介绍文档

## 🤔 什么是 RAG？

RAG（Retrieval-Augmented Generation，检索增强生成）是一种让大语言模型结合**外部知识库**回答问题的技术方案 🚀。

它的核心思路很直接：

```
用户问题 → 检索相关文档 → 拼接上下文 → LLM 生成答案
```

和只依赖模型参数的纯生成方式相比，RAG 能显著提升以下能力：

| 能力 | 说明 |
|------|------|
| 📚 私有知识接入 | 让模型理解企业内部文档、产品资料、知识库 |
| ⏱️ 知识实时更新 | 不用重新训练模型，只更新外部数据即可 |
| 🎯 降低幻觉 | 回答基于检索结果，减少“编造答案” |
| 🔍 可追溯 | 可以返回引用片段，方便人工校验 |

---

## 😮 为什么需要 RAG 库？

理论上，你完全可以手动把 RAG 的每一步自己拼起来，但真实工程里流程并不简单：

```
📄 文档加载
   ↓
✂️ 文本切分
   ↓
🔢 Embedding 向量化
   ↓
🗂️ 向量数据库写入
   ↓
🔍 检索召回
   ↓
🎯 重排序（Rerank）
   ↓
🧠 Prompt 组装
   ↓
🤖 LLM 生成答案
```

如果每个环节都自己开发，成本会很高。RAG 库的价值就在于把这些通用流程封装起来，帮助开发者更快搭建知识问答、企业搜索、文档助手和 AI Copilot 系统 ✅。

---

## 🏗️ 一个典型的 RAG 系统由什么组成？

### 1. 📄 文档加载器（Loader）
负责读取 PDF、Word、HTML、Markdown、数据库记录、网页内容等原始数据。

### 2. ✂️ 文本切分器（Text Splitter）
把长文档拆成适合向量检索的小块，避免上下文过长或语义过散。

### 3. 🔢 Embedding 模型
把文本转成向量，供相似度搜索使用。

### 4. 🗂️ 向量数据库（Vector Store）
用于存储文本向量并执行召回，例如：
- FAISS
- Chroma
- Milvus
- Qdrant
- Weaviate
- Pinecone

### 5. 🔍 检索器（Retriever）
从向量库或混合检索系统中找出最相关的片段。

### 6. 🎯 重排序器（Reranker）
对召回结果再次排序，提升最终命中质量。

### 7. 🤖 生成模块（LLM）
把问题和检索到的上下文交给大模型，输出最终答案。

---

## 🧰 主流 RAG 库有哪些？

目前最常见的 RAG 开发框架主要包括 **LangChain**、**LlamaIndex** 和 **Haystack**。它们并不互斥，但侧重点不同。

### 1. 🔗 LangChain

LangChain 是目前生态最丰富的 LLM 应用开发框架之一，RAG 只是它能力中的一部分。

**特点：**
- 🔌 集成广：模型、向量库、工具、Agent、工作流组件很多
- ⚙️ 灵活度高：适合搭建复杂链路
- 🚀 原型快：适合快速验证产品想法

**适合场景：**
- 需要把 RAG 和 Agent、Tool Calling、工作流编排结合
- 需要连接多种模型和基础设施

**注意点：**
- 抽象层较多，项目做大后调试复杂度会上升

### 2. 🦙 LlamaIndex

LlamaIndex 更聚焦于“**让大模型高效接入外部知识**”这件事，本质上非常适合文档问答类场景。

**特点：**
- 📚 数据接入体验好：文档读取、索引构建比较顺手
- 🧠 面向知识库：适合做文档检索、总结、问答
- 🧩 索引能力强：支持多种检索和查询模式

**适合场景：**
- 企业知识库问答
- 文档助手
- 多文档总结与检索

**注意点：**
- 如果要做很复杂的 Agent 编排，通常还需要和其他框架配合

### 3. 🌾 Haystack

Haystack 是较早成熟起来的检索与问答框架，在企业级搜索、问答系统中有较强工程感。

**特点：**
- 🏢 企业化风格明显：流程清晰、组件边界明确
- 🔍 检索导向强：适合严肃的搜索和问答场景
- 🧱 管道式架构：便于理解和控制

**适合场景：**
- 企业内部搜索
- 文档检索平台
- 对检索质量和部署可控性要求高的系统

**注意点：**
- 社区热度不如 LangChain 和 LlamaIndex 那么高

---

## ⚔️ 主流 RAG 库对比

| 维度 | LangChain | LlamaIndex | Haystack |
|------|-----------|------------|----------|
| 🎯 核心定位 | LLM 应用编排框架 | 知识接入与索引框架 | 检索/问答管道框架 |
| 🚀 上手速度 | 快 | 快 | 中等 |
| 📚 文档问答体验 | 好 | 很好 | 好 |
| 🔌 生态集成 | 很强 | 强 | 中等 |
| 🧠 Agent 能力 | 强 | 一般 | 一般 |
| 🏢 企业部署感 | 中等 | 中等 | 较强 |
| 👨‍💻 适合人群 | 全栈 AI 应用开发者 | 知识库应用开发者 | 搜索系统/企业开发团队 |

---

## 💡 如何选型？

如果你只是想快速做一个 Demo，通常优先考虑 **LangChain**，因为它连接组件很方便，开发速度快 ⚡。

如果你的核心需求是“把一堆文档变成可问答知识库”，通常 **LlamaIndex** 更顺手，因为它对索引和文档接入的支持更自然 📚。

如果你更在意流程可控、检索架构清晰、企业级部署能力，那么 **Haystack** 往往更合适 🏢。

可以用一句话概括：

```
LangChain 更像“总装平台”
LlamaIndex 更像“知识库专家”
Haystack 更像“企业检索管道”
```

---

## 🧪 LangChain RAG 示例

下面是一个典型的 LangChain RAG 示例：

```python
from langchain_openai import ChatOpenAI, OpenAIEmbeddings
from langchain_chroma import Chroma
from langchain_community.document_loaders import TextLoader
from langchain_text_splitters import RecursiveCharacterTextSplitter
from langchain.chains import RetrievalQA

# 📄 加载文档
loader = TextLoader("knowledge.txt", encoding="utf-8")
documents = loader.load()

# ✂️ 文本切分
splitter = RecursiveCharacterTextSplitter(
    chunk_size=500,
    chunk_overlap=100
)
docs = splitter.split_documents(documents)

# 🔢 建立向量索引
vectorstore = Chroma.from_documents(
    docs,
    embedding=OpenAIEmbeddings()
)

# 🔍 创建检索器
retriever = vectorstore.as_retriever(search_kwargs={"k": 3})

# 🤖 创建问答链
qa = RetrievalQA.from_chain_type(
    llm=ChatOpenAI(model="gpt-4o-mini"),
    retriever=retriever
)

# 💬 发起提问
result = qa.invoke({"query": "这个知识库主要讲了什么？"})
print(result["result"])
```

这个例子已经覆盖了一个最小 RAG 链路：

```
文档加载 → 文本切分 → 向量化 → 检索 → 生成
```

---

## 🦙 LlamaIndex 示例

如果使用 LlamaIndex，同样的事情通常可以写得更直接：

```python
from llama_index.core import VectorStoreIndex, SimpleDirectoryReader

# 📄 读取目录中的文档
documents = SimpleDirectoryReader("./data").load_data()

# 🏗️ 构建索引
index = VectorStoreIndex.from_documents(documents)

# 🔍 创建查询引擎
query_engine = index.as_query_engine()

# 💬 查询
response = query_engine.query("总结一下这些文档的核心内容")
print(response)
```

对于文档型问答系统，LlamaIndex 往往能让你更快跑通首版原型 ✅。

---

## 🚨 RAG 项目里真正重要的不是“库名”，而是这些细节

很多团队以为换一个框架，效果就会立刻提升。实际上，RAG 效果往往取决于以下环节：

| 关键点 | 说明 |
|--------|------|
| ✂️ Chunk 切分 | 切得太大影响召回，切得太碎会丢失上下文 |
| 🔢 Embedding 质量 | 向量模型是否适合你的业务语料非常关键 |
| 🔍 检索策略 | 仅向量检索、有无关键词检索、是否混合检索 |
| 🎯 Rerank | 是否对召回结果做二次排序 |
| 🧾 Prompt 设计 | 是否要求模型“只基于已检索内容回答” |
| 📏 评估机制 | 是否有命中率、准确率、引用质量等指标 |

也就是说，RAG 库只是一个工程加速器，不是效果保证器。

---

## 🌟 典型应用场景

- 🏢 企业知识库问答：员工直接查询制度、流程、产品资料
- 💬 智能客服：从 FAQ、工单、手册中检索答案
- 🔎 语义搜索：比关键词搜索更理解用户意图
- 🧑‍💻 代码助手：基于内部代码库和文档回答问题
- 📑 多文档总结：快速归纳多个文档的共同主题

---

## 🗺️ 架构总结

```
┌─────────────────────────────────────────────┐
│               📚 RAG 系统架构                │
│                                             │
│  ┌──────────┐    ┌──────────────────────┐  │
│  │ 📄 原始文档│───▶│   ✂️ 文档处理管道      │  │
│  └──────────┘    │  • 分块                │  │
│                  │  • 清洗                │  │
│                  │  • 向量化              │  │
│                  └──────────┬───────────┘  │
│                             │               │
│                    ┌────────▼────────┐      │
│                    │ 🗂️ 向量数据库     │      │
│                    │  + 元数据索引     │      │
│                    └────────┬────────┘      │
│                             │               │
│  ┌──────────┐    ┌──────────▼───────────┐  │
│  │ 💬 用户提问│───▶│   🔍 检索与重排引擎    │  │
│  └──────────┘    │  • Retriever         │  │
│                  │  • Reranker          │  │
│                  └──────────┬───────────┘  │
│                             │               │
│                    ┌────────▼────────┐      │
│                    │ 🤖 LLM 生成答案  │      │
│                    └─────────────────┘      │
└─────────────────────────────────────────────┘
```

---

## 📚 参考资源

- 📖 LangChain 官方文档：[python.langchain.com](https://python.langchain.com/)
- 🦙 LlamaIndex 官方文档：[docs.llamaindex.ai](https://docs.llamaindex.ai/)
- 🌾 Haystack 官方文档：[haystack.deepset.ai](https://haystack.deepset.ai/)
- 📄 RAG 经典论文：[Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks](https://arxiv.org/abs/2005.11401)
