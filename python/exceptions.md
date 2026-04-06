# ⚠️ Python 异常处理

## 🤔 什么是异常？

异常（Exception）是程序运行过程中出现的错误或异常情况。

例如：

```python
print(10 / 0)
```

这段代码运行时会报错，因为除数不能为 0。Python 会抛出一个异常：

```python
ZeroDivisionError
```

如果不处理异常，程序通常会直接中断。

---

## 😮 为什么要处理异常？

异常处理的目标，不是“让错误消失”，而是让程序在出错时仍然可控。

| 价值 | 说明 |
|------|------|
| 🛡️ 防止程序崩溃 | 出错时可以优雅处理 |
| 🧾 提供错误信息 | 方便定位问题 |
| 🔄 保证清理逻辑执行 | 例如关闭文件、释放连接 |
| 👨‍💻 提升用户体验 | 可以返回友好提示，而不是直接报错 |

---

## 🧪 常见异常类型

Python 内置了很多异常类型，开发中最常见的包括：

| 异常 | 说明 |
|------|------|
| `ZeroDivisionError` | 除零错误 |
| `TypeError` | 类型错误 |
| `ValueError` | 值不合法 |
| `IndexError` | 索引越界 |
| `KeyError` | 字典 key 不存在 |
| `FileNotFoundError` | 文件不存在 |
| `AttributeError` | 对象没有某个属性 |
| `ImportError` | 导入模块失败 |

示例：

```python
nums = [1, 2, 3]
print(nums[10])   # IndexError
```

---

## 🏗️ `try...except` 基本写法

Python 使用 `try...except` 捕获异常：

```python
try:
    result = 10 / 0
except ZeroDivisionError:
    print("除数不能为 0")
```

执行逻辑可以理解为：

```
先执行 try
如果出错，就进入 except
如果没出错，except 不执行
```

---

## 🎯 捕获指定异常

推荐尽量捕获具体异常，而不是直接“兜底”。

```python
try:
    num = int("abc")
except ValueError:
    print("输入内容不是合法整数")
```

这样代码更清晰，也更容易定位问题。

---

## 📦 捕获多个异常

你可以分别处理不同错误：

```python
try:
    nums = [1, 2, 3]
    print(nums[5])
except IndexError:
    print("列表索引越界")
except TypeError:
    print("类型错误")
```

也可以合并写法：

```python
try:
    value = int("abc")
except (ValueError, TypeError):
    print("数据转换失败")
```

---

## 🧠 获取异常对象

可以通过 `as e` 拿到具体异常信息：

```python
try:
    result = 10 / 0
except ZeroDivisionError as e:
    print("发生异常：", e)
```

这在记录日志或调试时非常有用。

---

## ✅ `else` 的作用

`else` 会在“没有异常时”执行。

```python
try:
    result = 10 / 2
except ZeroDivisionError:
    print("除零错误")
else:
    print("计算成功：", result)
```

适合放成功逻辑，让代码结构更清楚。

---

## 🔒 `finally` 的作用

`finally` 中的代码无论是否发生异常，通常都会执行。

```python
try:
    f = open("test.txt", "r", encoding="utf-8")
    content = f.read()
except FileNotFoundError:
    print("文件不存在")
finally:
    print("结束处理")
```

它常用于：

- 关闭文件
- 关闭数据库连接
- 释放锁
- 清理资源

---

## 🚨 `raise` 主动抛出异常

除了捕获异常，我们也可以主动抛出异常。

```python
def divide(a, b):
    if b == 0:
        raise ValueError("b 不能为 0")
    return a / b
```

调用：

```python
print(divide(10, 2))
```

如果传入非法值，程序会抛出 `ValueError`。

---

## 🧱 自定义异常

当内置异常不足以表达业务错误时，可以自定义异常类。

```python
class LoginError(Exception):
    pass

def login(username, password):
    if username != "admin" or password != "123456":
        raise LoginError("用户名或密码错误")
```

捕获：

```python
try:
    login("tom", "111")
except LoginError as e:
    print(e)
```

这样代码会更贴近业务语义。

---

## ⚔️ 常见错误处理方式对比

| 方式 | 说明 |
|------|------|
| 不处理异常 | 程序直接报错中断 |
| 捕获具体异常 | 推荐，清晰且可控 |
| 捕获所有异常 | 可以兜底，但要谨慎 |
| 主动抛出异常 | 适合业务校验失败场景 |

---

## 🧪 一个综合示例

```python
def read_number(filename):
    try:
        with open(filename, "r", encoding="utf-8") as f:
            content = f.read().strip()
            return int(content)
    except FileNotFoundError:
        print("文件不存在")
    except ValueError:
        print("文件内容不是整数")
    else:
        print("读取成功")
    finally:
        print("读取流程结束")

result = read_number("number.txt")
print("结果：", result)
```

这个例子里涉及了：

- `try`：尝试执行代码
- `except`：处理不同异常
- `else`：成功时执行
- `finally`：无论如何都执行

---

## 📌 异常处理的实践建议

1. 优先捕获具体异常，不要上来就写 `except Exception`
2. 不要把异常吞掉，至少打印日志或返回清晰信息
3. 资源清理尽量放在 `finally` 或 `with` 语句中
4. 业务校验失败时，可以用 `raise` 主动抛出异常

异常处理写得好，程序的稳定性和可维护性会明显提升。

---

## 📚 参考资源

- 📖 Python 官方文档：[docs.python.org](https://docs.python.org/3/)
- 🎓 Python Tutorial - Errors and Exceptions：[docs.python.org/tutorial/errors.html](https://docs.python.org/3/tutorial/errors.html)
- 💻 内置异常文档：[docs.python.org/library/exceptions.html](https://docs.python.org/3/library/exceptions.html)
