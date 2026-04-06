# 🐍 Python 数据类型与函数

## 🤔 什么是 Python 数据类型？

在 Python 里，数据类型（Data Type）决定了一个值可以保存什么内容，以及它支持哪些操作。

例如：

```python
name = "Alice"
age = 18
price = 19.9
is_active = True
```

这几个变量分别对应不同的数据类型：

| 值 | 类型 |
|----|------|
| `"Alice"` | `str` |
| `18` | `int` |
| `19.9` | `float` |
| `True` | `bool` |

Python 是一种**动态类型语言**，也就是说，变量在运行时决定类型，不需要像 Java 或 C++ 那样先声明类型 ✅。

---

## 🧩 Python 常见数据类型

Python 的数据类型大致可以分为以下几类：

### 1. 🔢 数值类型

| 类型 | 说明 | 示例 |
|------|------|------|
| `int` | 整数 | `10`、`-3` |
| `float` | 浮点数 | `3.14`、`0.5` |
| `complex` | 复数 | `3+4j` |

示例：

```python
a = 10
b = 3.14
c = 2 + 5j
```

### 2. 🔤 字符串 `str`

字符串用于表示文本内容。

```python
title = "Python"
message = 'Hello World'
```

常见操作：

```python
name = "Tom"
print(name.upper())      # TOM
print(name.lower())      # tom
print(name[0])           # T
print(name + " Lee")     # Tom Lee
```

### 3. ✅ 布尔类型 `bool`

布尔值只有两个：

```python
True
False
```

常用于条件判断：

```python
is_login = True

if is_login:
    print("已登录")
```

### 4. 📋 列表 `list`

列表是**有序、可变**的集合类型。

```python
nums = [1, 2, 3]
names = ["Tom", "Jerry", "Alice"]
```

常见操作：

```python
nums.append(4)
nums.remove(2)
print(nums[0])
print(len(nums))
```

### 5. 📦 元组 `tuple`

元组是**有序、不可变**的集合类型。

```python
point = (10, 20)
colors = ("red", "green", "blue")
```

适合表示不会变化的数据。

### 6. 🧺 集合 `set`

集合是**无序、元素唯一**的类型，适合去重。

```python
tags = {"python", "fastapi", "python"}
print(tags)   # {'python', 'fastapi'}
```

### 7. 🗂️ 字典 `dict`

字典用来保存**键值对**数据，是 Python 中非常常用的类型。

```python
user = {
    "name": "Alice",
    "age": 20,
    "city": "Shanghai"
}
```

常见操作：

```python
print(user["name"])
user["age"] = 21
user["email"] = "alice@example.com"
```

---

## ⚔️ 可变类型 vs 不可变类型

理解这一点对 Python 非常重要：

| 类型类别 | 示例 |
|----------|------|
| 可变类型 | `list`、`dict`、`set` |
| 不可变类型 | `int`、`float`、`str`、`tuple`、`bool` |

示例：

```python
nums = [1, 2, 3]
nums.append(4)   # 可以修改

name = "Tom"
# name[0] = "J"  # 不可以，字符串不可变
```

---

## 🔄 类型转换

Python 提供了很多内置函数用于类型转换：

| 函数 | 作用 |
|------|------|
| `int()` | 转整数 |
| `float()` | 转浮点数 |
| `str()` | 转字符串 |
| `list()` | 转列表 |
| `tuple()` | 转元组 |
| `set()` | 转集合 |
| `dict()` | 转字典 |

示例：

```python
age = int("18")
price = float("19.9")
text = str(100)
chars = list("python")
```

---

## 🤔 什么是函数？

函数（Function）是一段**可以重复使用的代码逻辑**。把一组操作封装起来，在需要的时候调用，可以提高代码复用性和可读性。

例如：

```python
def say_hello():
    print("Hello")
```

调用函数：

```python
say_hello()
```

---

## 🏗️ 如何定义函数？

Python 使用 `def` 定义函数：

```python
def add(a, b):
    return a + b
```

调用：

```python
result = add(3, 5)
print(result)   # 8
```

函数的基本结构可以理解为：

```
def 函数名(参数):
    函数体
    return 返回值
```

---

## 📥 函数参数

### 1. 普通参数

```python
def greet(name):
    print("Hello,", name)
```

### 2. 默认参数

```python
def greet(name, city="Beijing"):
    print(name, city)
```

### 3. 关键字参数

```python
def introduce(name, age):
    print(name, age)

introduce(age=18, name="Tom")
```

### 4. 可变参数 `*args`

用于接收多个位置参数。

```python
def total(*args):
    return sum(args)

print(total(1, 2, 3, 4))
```

### 5. 关键字可变参数 `**kwargs`

用于接收多个键值参数。

```python
def show_user(**kwargs):
    print(kwargs)

show_user(name="Alice", age=20)
```

---

## 📤 返回值

函数可以通过 `return` 返回结果：

```python
def square(x):
    return x * x
```

如果没有显式写 `return`，函数默认返回 `None`。

```python
def demo():
    print("test")

print(demo())   # None
```

---

## 🧠 作用域

函数内部定义的变量，默认只能在函数内部使用，这叫做**局部变量**。

```python
def test():
    x = 10
    print(x)
```

函数外部定义的变量，通常叫**全局变量**。

```python
name = "Python"

def show():
    print(name)
```

---

## 🧪 常见内置函数

Python 自带很多常用函数，开发中会频繁使用：

| 函数 | 作用 |
|------|------|
| `print()` | 输出内容 |
| `len()` | 获取长度 |
| `type()` | 查看类型 |
| `input()` | 获取输入 |
| `sum()` | 求和 |
| `max()` | 最大值 |
| `min()` | 最小值 |
| `range()` | 生成整数序列 |
| `sorted()` | 排序 |
| `abs()` | 取绝对值 |

示例：

```python
nums = [3, 1, 5, 2]

print(len(nums))
print(max(nums))
print(min(nums))
print(sorted(nums))
```

---

## 🌟 一个综合示例

下面这个例子把数据类型和函数结合起来：

```python
students = [
    {"name": "Tom", "score": 88},
    {"name": "Alice", "score": 95},
    {"name": "Jerry", "score": 76}
]

def get_average(data):
    total = 0
    for item in data:
        total += item["score"]
    return total / len(data)

def get_top_student(data):
    return max(data, key=lambda item: item["score"])

avg = get_average(students)
top = get_top_student(students)

print("平均分:", avg)
print("最高分学生:", top)
```

这个示例里涉及了：

- `list`：保存多个学生
- `dict`：保存学生属性
- `int`：保存分数
- `function`：封装平均分和最高分逻辑

---

## 📌 学习建议

对于 Python 初学者，数据类型和函数是最基础也最重要的部分。建议按下面的顺序掌握：

1. 先理解 `int`、`float`、`str`、`bool`
2. 再掌握 `list`、`tuple`、`set`、`dict`
3. 学会类型转换和常见内置函数
4. 最后重点练习函数定义、参数、返回值和作用域

把这些内容掌握好之后，再学习面向对象、文件操作、异常处理和模块化开发会轻松很多。

---

## 📚 参考资源

- 📖 Python 官方文档：[docs.python.org](https://docs.python.org/3/)
- 🎓 Python Tutorial：[docs.python.org/tutorial](https://docs.python.org/3/tutorial/)
- 💻 Python 内置函数文档：[docs.python.org/library/functions.html](https://docs.python.org/3/library/functions.html)
