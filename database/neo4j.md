# 🗄️ Neo4j 介绍

## 🤔 什么是 Neo4j？

Neo4j 是全球最流行的**原生图数据库（Native Graph Database）** 🏆，由瑞典公司 Neo4j Inc. 于 2007 年创建。它专为存储、查询和分析**高度关联的数据**而设计，在处理复杂关系网络时比传统关系型数据库快 1000 倍以上 ⚡。

> 💬 核心理念：**关系是一等公民（Relationships are first-class citizens）**

---

## 🧠 图数据库 vs 关系型数据库

### 😰 关系型数据库的痛点

当数据关系变得复杂时，SQL 的多表 JOIN 查询会指数级膨胀：

```sql
-- 😵 找出"朋友的朋友的朋友"买过什么商品
SELECT DISTINCT p.name
FROM users u1
JOIN friendships f1 ON u1.id = f1.user_id
JOIN users u2 ON f1.friend_id = u2.id
JOIN friendships f2 ON u2.id = f2.user_id
JOIN users u3 ON f2.friend_id = u3.id
JOIN purchases pu ON u3.id = pu.user_id
JOIN products p ON pu.product_id = p.id
WHERE u1.name = 'Alice';
```

### 😎 图数据库的优雅

同样的查询，用图数据库的 Cypher 语言只需一行：

```cypher
// ✅ 简洁、直观、高效
MATCH (:User {name: 'Alice'})-[:FRIEND*3]->(u)-[:PURCHASED]->(p:Product)
RETURN DISTINCT p.name
```

---

## 🏗️ 核心概念

Neo4j 的数据模型由 4 个基本元素构成：

### 1. 🔵 节点（Node）
图中的实体，相当于关系数据库中的"行"。

```
(张三:Person {age: 28, city: "北京"})
(苹果公司:Company {founded: 1976, country: "USA"})
```

### 2. ➡️ 关系（Relationship）
连接两个节点的有向边，**必须有类型**，可以携带属性。

```
(张三)-[:WORKS_AT {since: 2020}]->(苹果公司)
(张三)-[:KNOWS {since: 2018}]->(李四)
```

### 3. 🏷️ 标签（Label）
节点的分类标识，一个节点可以有多个标签。

```
(小明:Person:Student:VIP)
```

### 4. 📦 属性（Property）
节点和关系上的键值对数据。

```
{name: "张三", age: 28, active: true}
```

---

## 🔤 Cypher 查询语言

Cypher 是 Neo4j 的声明式查询语言，语法直观，像"画图"一样写查询 🎨。

### 基本语法结构

```
(节点)-[:关系]->(节点)
```

### 📝 常用操作示例

**创建数据**
```cypher
// ✏️ 创建节点
CREATE (p:Person {name: '张三', age: 28})

// ✏️ 创建关系
MATCH (a:Person {name: '张三'}), (b:Person {name: '李四'})
CREATE (a)-[:KNOWS {since: 2020}]->(b)
```

**查询数据**
```cypher
// 🔍 查找所有 Person 节点
MATCH (p:Person)
RETURN p.name, p.age

// 🔍 查找张三的所有朋友
MATCH (:Person {name: '张三'})-[:KNOWS]->(friend:Person)
RETURN friend.name

// 🔍 查找 2 度以内的关系链
MATCH path = (:Person {name: '张三'})-[:KNOWS*1..2]->(p:Person)
RETURN path
```

**更新数据**
```cypher
// 🔄 更新属性
MATCH (p:Person {name: '张三'})
SET p.age = 29, p.city = '上海'

// 🔄 添加标签
MATCH (p:Person {name: '张三'})
SET p:VIP
```

**删除数据**
```cypher
// 🗑️ 删除节点（需先删除关系）
MATCH (p:Person {name: '张三'})
DETACH DELETE p
```

---

## ⚡ 核心优势

| 优势 | 说明 |
|------|------|
| 🚀 极速关系查询 | 原生图存储，关系查询不需要 JOIN，直接指针跳转 |
| 📐 灵活 Schema | 无需预定义严格表结构，随时扩展属性和关系 |
| 🔄 ACID 事务 | 支持完整的事务特性，保证数据一致性 |
| 📊 可视化 | 内置 Browser 界面，图形化展示数据网络 |
| 🌐 水平扩展 | 企业版支持集群和分片部署 |
| 🔌 生态丰富 | 支持 Python、Java、JavaScript 等多语言驱动 |

---

## 🌟 典型应用场景

### 👥 社交网络
```cypher
// 找出共同好友
MATCH (a:User {name: 'Alice'})-[:FRIEND]->(common)<-[:FRIEND]-(b:User {name: 'Bob'})
RETURN common.name AS 共同好友
```

### 🛒 推荐系统
```cypher
// 协同过滤：喜欢同款商品的用户还买了什么
MATCH (u:User {id: 1})-[:PURCHASED]->(p:Product)<-[:PURCHASED]-(other:User)
      -[:PURCHASED]->(rec:Product)
WHERE NOT (u)-[:PURCHASED]->(rec)
RETURN rec.name, COUNT(*) AS score
ORDER BY score DESC LIMIT 10
```

### 🔐 欺诈检测
```cypher
// 发现共享同一设备的多个账户（疑似欺诈）
MATCH (a1:Account)-[:USED]->(d:Device)<-[:USED]-(a2:Account)
WHERE a1 <> a2
RETURN d.id AS 设备, COLLECT(a1.id) AS 关联账户
```

### 🗺️ 路径规划
```cypher
// 最短路径
MATCH path = shortestPath(
  (:City {name: '北京'})-[:CONNECTED*]-(:City {name: '上海'})
)
RETURN path
```

### 🧬 知识图谱 / GraphRAG
```cypher
// 查找与某实体相关的所有知识
MATCH (e:Entity {name: 'GPT-4'})-[r]-(related)
RETURN e, r, related
```

---

## 🚀 快速上手

### 方式一：Docker 启动（推荐 🌟）
```bash
docker run \
  --name neo4j \
  -p 7474:7474 -p 7687:7687 \
  -e NEO4J_AUTH=neo4j/password123 \
  neo4j:latest
```
浏览器访问 👉 `http://localhost:7474`

### 方式二：本地安装
```bash
# macOS
brew install neo4j
neo4j start

# 访问 Browser
open http://localhost:7474
```

### Python 驱动示例
```python
from neo4j import GraphDatabase

# 🔌 建立连接
driver = GraphDatabase.driver(
    "bolt://localhost:7687",
    auth=("neo4j", "password123")
)

# 📝 写入数据
def create_person(tx, name, age):
    tx.run(
        "CREATE (p:Person {name: $name, age: $age})",
        name=name, age=age
    )

# 🔍 查询数据
def find_friends(tx, name):
    result = tx.run(
        "MATCH (:Person {name: $name})-[:KNOWS]->(f) RETURN f.name",
        name=name
    )
    return [record["f.name"] for record in result]

with driver.session() as session:
    session.execute_write(create_person, "张三", 28)
    friends = session.execute_read(find_friends, "张三")
    print(friends)

driver.close()
```

---

## 🧰 生态工具

| 工具 | 用途 |
|------|------|
| 🖥️ Neo4j Browser | 内置可视化查询界面 |
| 🌸 Neo4j Bloom | 业务人员友好的图探索工具 |
| 📊 Neo4j Data Importer | CSV/JSON 数据导入工具 |
| 🔬 Graph Data Science (GDS) | 图算法库（PageRank、社区检测等） |
| 🤖 Neo4j & LangChain | LLM + 图数据库集成 |
| ☁️ AuraDB | Neo4j 官方云托管服务 |

---

## 🔗 Neo4j 与 GraphRAG 的结合

Neo4j 天然适合作为 GraphRAG 的知识图谱存储后端 🤝：

```python
# LangChain + Neo4j + GraphRAG 典型架构
from langchain_community.graphs import Neo4jGraph
from langchain_community.vectorstores import Neo4jVector

# 存储实体关系
graph = Neo4jGraph(url="bolt://localhost:7687", ...)

# 存储向量嵌入（支持向量 + 图混合检索）
vector_store = Neo4jVector.from_documents(
    documents,
    embedding=OpenAIEmbeddings(),
    graph=graph
)
```

**组合优势：**
- 🕸️ 图结构存储实体关系 → 支持多跳推理
- 🔢 向量索引存储语义嵌入 → 支持语义检索
- 🔄 两者统一在一个数据库中 → 简化架构

---

## 📊 Neo4j 版本对比

| 版本 | 特性 | 适合 |
|------|------|------|
| 🆓 Community | 基础图功能、单节点 | 开发/学习 |
| 💼 Enterprise | 集群、细粒度权限、热备份 | 生产环境 |
| ☁️ AuraDB Free | 云托管、免费额度 | 快速原型 |
| ☁️ AuraDB Pro | 云托管、生产级 SLA | 云上生产 |

---

## 📚 参考资源

- 📖 官方文档：[neo4j.com/docs](https://neo4j.com/docs/)
- 🎓 免费课程：[graphacademy.neo4j.com](https://graphacademy.neo4j.com/)
- 💻 GitHub：[github.com/neo4j](https://github.com/neo4j)
- 🧪 在线沙箱：[sandbox.neo4j.com](https://sandbox.neo4j.com/)
- 📄 Cypher 手册：[neo4j.com/docs/cypher-manual](https://neo4j.com/docs/cypher-manual/current/)
