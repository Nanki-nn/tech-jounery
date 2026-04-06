# 📁 Python 文件操作

## 🤔 什么是文件操作？

文件操作就是使用程序去读取、写入、修改和管理磁盘上的文件。

在 Python 开发中，文件操作非常常见，例如：

- 📄 读取配置文件
- 📝 写入日志
- 📊 处理 CSV / JSON 数据
- 🖼️ 读取图片、音频、视频等二进制文件
- 🗂️ 管理目录和批量处理文件

---

## 🧩 Python 中文件操作的核心步骤

文件操作通常分为三个步骤：

```
打开文件 → 读写文件 → 关闭文件
```

最基础的写法：

```python
f = open("demo.txt", "r", encoding="utf-8")
content = f.read()
f.close()
```

不过在实际开发中，更推荐使用 `with open(...)`。

---

## 🏗️ `open()` 函数

Python 使用 `open()` 打开文件。

```python
open(file, mode="r", encoding=None)
```

常用参数：

| 参数 | 说明 |
|------|------|
| `file` | 文件路径 |
| `mode` | 打开模式 |
| `encoding` | 编码方式，文本文件常用 `utf-8` |

---

## 🔓 常见打开模式

| 模式 | 说明 |
|------|------|
| `r` | 只读，文件不存在会报错 |
| `w` | 只写，会覆盖原文件 |
| `a` | 追加写入 |
| `x` | 新建文件，如果文件已存在会报错 |
| `b` | 二进制模式 |
| `t` | 文本模式，默认值 |
| `+` | 读写模式 |

常见组合：

| 模式 | 说明 |
|------|------|
| `rb` | 以二进制只读方式打开 |
| `wb` | 以二进制写入方式打开 |
| `r+` | 读写 |
| `a+` | 追加并可读取 |

---

## 📖 读取文件

### 1. `read()`

一次性读取全部内容。

```python
with open("demo.txt", "r", encoding="utf-8") as f:
    content = f.read()
    print(content)
```

### 2. `readline()`

每次读取一行。

```python
with open("demo.txt", "r", encoding="utf-8") as f:
    line = f.readline()
    print(line)
```

### 3. `readlines()`

读取所有行，返回列表。

```python
with open("demo.txt", "r", encoding="utf-8") as f:
    lines = f.readlines()
    print(lines)
```

### 4. 逐行遍历

这是更常见也更节省内存的方式：

```python
with open("demo.txt", "r", encoding="utf-8") as f:
    for line in f:
        print(line.strip())
```

---

## 📝 写入文件

### 1. `write()`

```python
with open("demo.txt", "w", encoding="utf-8") as f:
    f.write("Hello Python\n")
    f.write("File Operation\n")
```

### 2. `writelines()`

```python
lines = ["Tom\n", "Alice\n", "Jerry\n"]

with open("names.txt", "w", encoding="utf-8") as f:
    f.writelines(lines)
```

注意：`writelines()` 不会自动补换行符，需要自己写 `\n`。

---

## ➕ 追加写入

如果不想覆盖原文件，可以使用 `a` 模式。

```python
with open("log.txt", "a", encoding="utf-8") as f:
    f.write("new log message\n")
```

这在日志记录场景中非常常见。

---

## 🔒 为什么推荐 `with open()`？

因为 `with` 语句会在代码块结束后自动关闭文件，即使中间发生异常，也能正确释放资源。

```python
with open("demo.txt", "r", encoding="utf-8") as f:
    content = f.read()
```

相比手动 `close()`，这种写法更安全、更推荐 ✅。

---

## 🖼️ 二进制文件操作

读取图片、音频、视频等文件时，通常要使用二进制模式。

```python
with open("image.png", "rb") as f:
    data = f.read()
```

写入二进制文件：

```python
with open("copy.png", "wb") as f:
    f.write(data)
```

---

## 🗂️ 文件路径操作

Python 里常见的路径处理方式有两种：

- 使用 `os`
- 使用 `pathlib`

### `os.path` 示例

```python
import os

path = "data/demo.txt"

print(os.path.exists(path))
print(os.path.isfile(path))
print(os.path.dirname(path))
print(os.path.basename(path))
```

### `pathlib` 示例

```python
from pathlib import Path

path = Path("data/demo.txt")

print(path.exists())
print(path.name)
print(path.parent)
print(path.suffix)
```

现代 Python 项目里，通常更推荐 `pathlib`。

---

## 📂 目录操作

可以用 `os` 或 `pathlib` 管理目录。

### 创建目录

```python
from pathlib import Path

Path("logs").mkdir(exist_ok=True)
```

### 遍历目录

```python
from pathlib import Path

for file in Path(".").iterdir():
    print(file)
```

### 获取某类文件

```python
from pathlib import Path

for file in Path("data").glob("*.txt"):
    print(file)
```

---

## ⚠️ 文件操作中的常见异常

文件操作很容易出现异常，例如：

| 异常 | 说明 |
|------|------|
| `FileNotFoundError` | 文件不存在 |
| `PermissionError` | 没有权限访问 |
| `IsADirectoryError` | 把目录当文件打开 |
| `UnicodeDecodeError` | 编码不匹配 |

示例：

```python
try:
    with open("not_exist.txt", "r", encoding="utf-8") as f:
        print(f.read())
except FileNotFoundError:
    print("文件不存在")
```

---

## 🌟 一个综合示例

```python
from pathlib import Path

data_dir = Path("data")
data_dir.mkdir(exist_ok=True)

file_path = data_dir / "users.txt"

with open(file_path, "w", encoding="utf-8") as f:
    f.write("Tom\n")
    f.write("Alice\n")
    f.write("Jerry\n")

with open(file_path, "r", encoding="utf-8") as f:
    users = [line.strip() for line in f]

print("用户列表：", users)
print("文件是否存在：", file_path.exists())
print("文件后缀：", file_path.suffix)
```

这个例子里同时涉及了：

- 文件写入
- 文件读取
- 路径拼接
- 目录创建
- 列表推导式处理文本

---

## 📌 实践建议

1. 文本文件优先使用 `with open(..., encoding="utf-8")`
2. 大文件读取时尽量逐行处理，不要一次性全部读入
3. 路径处理优先考虑 `pathlib`
4. 写文件前明确是覆盖写入还是追加写入
5. 文件操作配合异常处理一起使用更稳妥

文件操作是 Python 开发中的基础能力，熟练之后在日志处理、数据清洗、配置管理和脚本开发里都会非常常用。

---

## 📚 参考资源

- 📖 Python 官方文档：[docs.python.org](https://docs.python.org/3/)
- 🎓 Python Tutorial - Reading and Writing Files：[docs.python.org/tutorial/inputoutput.html#reading-and-writing-files](https://docs.python.org/3/tutorial/inputoutput.html#reading-and-writing-files)
- 💻 `pathlib` 文档：[docs.python.org/library/pathlib.html](https://docs.python.org/3/library/pathlib.html)
