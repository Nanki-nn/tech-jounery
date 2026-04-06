# ⚡ FastAPI 介绍文档

## 🤔 什么是 FastAPI？

FastAPI 是一个用于构建 **Python Web API** 的现代框架 🚀，由 Sebastián Ramírez 开发。它基于 Python 类型注解，强调**高性能、开发效率和自动文档生成**。

如果用一句话概括：

```
FastAPI = Python 里的现代高性能 API 框架
```

它特别适合下面这些场景：

- 🌐 构建 RESTful API
- 🤖 构建 AI / LLM 服务接口
- 🔌 微服务开发
- 📊 数据服务与后台接口
- ⚙️ 内部管理系统 API

---

## 😮 为什么 Python 开发者喜欢 FastAPI？

在 FastAPI 出现之前，Python Web API 常见选择主要是 Flask 和 Django REST Framework。

FastAPI 之所以流行，是因为它把几个关键能力结合得很好：

| 能力 | 说明 |
|------|------|
| ⚡ 高性能 | 基于 ASGI，性能明显优于传统 WSGI 框架 |
| 🧠 类型驱动 | 充分利用 Python 类型注解做参数校验和提示 |
| 📝 自动文档 | 自动生成 Swagger UI 和 ReDoc |
| ✅ 自动校验 | 请求参数、路径参数、Body 自动验证 |
| 🚀 开发效率高 | 写法简洁，适合快速构建接口 |

很多团队在做 AI 服务、中台接口、数据平台时，会优先选择 FastAPI，而不是更重的全栈框架 ✅。

---

## 🏗️ FastAPI 的核心组成

### 1. 🌐 路由（Path Operation）
FastAPI 通过装饰器声明接口，例如：

```python
@app.get("/users")
def get_users():
    return [{"name": "Tom"}]
```

### 2. 🧾 类型注解（Type Hints）
FastAPI 会读取函数参数上的类型注解，自动完成：
- 参数解析
- 数据校验
- 接口文档生成

### 3. 📦 Pydantic 模型
FastAPI 通常用 Pydantic 描述请求和响应的数据结构。

```python
from pydantic import BaseModel

class User(BaseModel):
    name: str
    age: int
```

### 4. 📘 自动文档
启动服务后可以直接访问：

- Swagger UI: `http://127.0.0.1:8000/docs`
- ReDoc: `http://127.0.0.1:8000/redoc`

---

## ⚔️ FastAPI vs Flask vs Django

| 维度 | FastAPI | Flask | Django |
|------|---------|-------|--------|
| ⚡ 性能 | 高 | 中等 | 中等 |
| 🧠 类型校验 | 原生支持 | 需自行扩展 | 一般 |
| 📝 自动文档 | 原生支持 | 通常需插件 | 需额外配置 |
| 🔌 API 开发体验 | 很强 | 灵活但较原始 | 偏全栈 |
| 🏢 适合场景 | API / 微服务 / AI 服务 | 小型服务 | 大型全栈网站 |

可以简单理解为：

```
Flask 更轻
Django 更全
FastAPI 更现代，尤其适合 API
```

---

## 🚀 快速上手

### 安装

```bash
pip install fastapi uvicorn
```

### 第一个 FastAPI 程序

```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def read_root():
    return {"message": "Hello FastAPI"}

@app.get("/items/{item_id}")
def read_item(item_id: int):
    return {"item_id": item_id}
```

### 启动服务

```bash
uvicorn main:app --reload
```

启动后访问：

- `http://127.0.0.1:8000/`
- `http://127.0.0.1:8000/docs`

---

## 🧪 常见开发方式

### 1. 📍 路径参数

```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/users/{user_id}")
def get_user(user_id: int):
    return {"user_id": user_id}
```

这里的 `user_id: int` 会自动校验。如果传入字符串且不能转换为整数，FastAPI 会直接返回错误响应。

### 2. 🔎 查询参数

```python
@app.get("/search")
def search(keyword: str, page: int = 1):
    return {"keyword": keyword, "page": page}
```

请求示例：

```bash
GET /search?keyword=python&page=2
```

### 3. 📦 请求体 Body

```python
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()

class Item(BaseModel):
    name: str
    price: float
    in_stock: bool = True

@app.post("/items")
def create_item(item: Item):
    return {"item": item}
```

FastAPI 会自动检查传入 JSON 是否符合 `Item` 模型定义。

---

## 🧠 Pydantic 为什么重要？

FastAPI 的一个关键优势就是和 Pydantic 配合得非常好。

Pydantic 的价值主要体现在：

| 能力 | 说明 |
|------|------|
| ✅ 数据校验 | 自动检查字段类型是否合法 |
| 🔄 数据转换 | 例如把字符串转换为数字、日期等 |
| 🧾 结构定义 | 明确请求和响应的数据格式 |
| 🧠 开发体验 | IDE 补全更友好，代码可维护性更高 |

示例：

```python
from pydantic import BaseModel, Field

class Product(BaseModel):
    name: str = Field(min_length=2, max_length=50)
    price: float = Field(gt=0)
    stock: int = Field(ge=0)
```

这类写法能让接口层的输入约束变得非常清晰。

---

## 🔐 FastAPI 常见能力

除了基本接口开发，FastAPI 还支持很多企业项目常用能力：

- 🔑 OAuth2 / JWT 鉴权
- 📁 文件上传
- ⏱️ 异步接口
- 🧩 依赖注入（Depends）
- 🗃️ 数据库集成（SQLAlchemy、Tortoise ORM 等）
- 🧪 测试支持（pytest + TestClient）
- 🌍 CORS 中间件

---

## ⚡ 异步能力为什么重要？

FastAPI 基于 **ASGI**，天然支持异步编程，这使它在处理高并发 I/O 场景时更有优势。

例如：

```python
from fastapi import FastAPI
import asyncio

app = FastAPI()

@app.get("/wait")
async def wait_demo():
    await asyncio.sleep(1)
    return {"status": "done"}
```

对于外部 API 调用、数据库访问、消息队列、LLM 请求这类 I/O 密集型业务，异步接口通常更合适。

---

## 🌟 FastAPI 的典型应用场景

- 🤖 AI 接口服务：封装模型推理、Embedding、RAG 查询接口
- 📊 数据平台 API：向前端或内部系统提供数据服务
- 🔌 微服务：拆分业务模块，提供标准 HTTP 接口
- 🛒 电商后台：商品、订单、库存等业务接口
- 🧑‍💻 内部工具平台：权限、日志、审批、报表接口

---

## 🗺️ 架构示意

```
┌─────────────────────────────────────────────┐
│              ⚡ FastAPI 应用架构              │
│                                             │
│  ┌──────────┐    ┌──────────────────────┐  │
│  │ 🌍 客户端   │───▶│   🌐 FastAPI 路由层     │  │
│  └──────────┘    │  • Path Params        │  │
│                  │  • Query Params       │  │
│                  │  • Request Body       │  │
│                  └──────────┬───────────┘  │
│                             │               │
│                    ┌────────▼────────┐      │
│                    │ 🧠 Pydantic 校验 │      │
│                    │  + 数据解析       │      │
│                    └────────┬────────┘      │
│                             │               │
│                    ┌────────▼────────┐      │
│                    │ ⚙️ 业务逻辑层     │      │
│                    │  + 服务调用       │      │
│                    └────────┬────────┘      │
│                             │               │
│                    ┌────────▼────────┐      │
│                    │ 🗃️ 数据库 / LLM  │      │
│                    │  / 外部服务       │      │
│                    └─────────────────┘      │
└─────────────────────────────────────────────┘
```

---

## 📌 总结

FastAPI 之所以在 Python 社区里迅速流行，本质上是因为它同时解决了几个关键问题：

- 写 API 足够快
- 类型校验足够强
- 文档生成足够方便
- 性能足够好

对于今天大量的 Python API 项目，尤其是 AI 服务、内部平台和微服务系统，FastAPI 已经成为非常主流的选择。

---

## 📚 参考资源

- 📖 官方文档：[fastapi.tiangolo.com](https://fastapi.tiangolo.com/)
- 💻 GitHub：[github.com/fastapi/fastapi](https://github.com/fastapi/fastapi)
- 🧪 Uvicorn：[www.uvicorn.org](https://www.uvicorn.org/)
- 📦 Pydantic：[docs.pydantic.dev](https://docs.pydantic.dev/)
