# 🧱 Python 面向对象

## 🤔 什么是面向对象编程？

面向对象编程（Object-Oriented Programming，OOP）是一种组织代码的思想。它把现实世界中的事物抽象成“**对象**”，再通过“**类**”描述这些对象的共同属性和行为。

例如：

```python
name = "Tom"
age = 18
```

上面只是零散的数据。如果用面向对象方式表达，可以写成：

```python
class Student:
    def __init__(self, name, age):
        self.name = name
        self.age = age
```

这样就能把“学生”作为一个整体来管理 ✅。

---

## 🧩 为什么要学面向对象？

当程序越来越大时，单纯依赖函数和变量会变得难以维护。面向对象能帮助我们：

| 优势 | 说明 |
|------|------|
| 🧱 更好组织代码 | 把相关数据和行为放在一起 |
| ♻️ 提高复用性 | 通过继承和组合复用已有逻辑 |
| 🔒 增强封装性 | 隐藏内部实现细节 |
| 🧠 更贴近现实建模 | 更容易描述“用户、订单、商品”等业务对象 |

---

## 🏗️ 类和对象

### 1. 类（Class）

类可以理解为“模板”或“图纸”，描述某一类事物的共同特征。

```python
class Dog:
    pass
```

### 2. 对象（Object）

对象是类创建出来的具体实例。

```python
class Dog:
    pass

dog1 = Dog()
dog2 = Dog()
```

这里的 `Dog` 是类，`dog1` 和 `dog2` 是对象。

---

## 📦 属性和方法

类里通常有两类内容：

- 属性：描述对象“有什么”
- 方法：描述对象“能做什么”

示例：

```python
class Dog:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def bark(self):
        print(self.name, "正在叫")
```

使用：

```python
dog = Dog("Buddy", 3)
print(dog.name)
print(dog.age)
dog.bark()
```

---

## 🧠 `__init__` 构造方法

`__init__` 是类的初始化方法，在创建对象时会自动执行。

```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age
```

创建对象时：

```python
p = Person("Alice", 20)
```

本质上会自动调用：

```python
Person.__init__(p, "Alice", 20)
```

---

## 👤 `self` 是什么？

`self` 代表“当前对象本身”。

```python
class User:
    def __init__(self, name):
        self.name = name

    def show_name(self):
        print(self.name)
```

当执行：

```python
u = User("Tom")
u.show_name()
```

本质上类似：

```python
User.show_name(u)
```

所以 `self` 用来访问当前对象的属性和方法。

---

## 🔒 封装

封装（Encapsulation）指的是把数据和操作数据的方法放在一起，并对内部细节进行保护。

Python 没有严格的私有属性机制，但通常用下划线表示“内部使用”：

```python
class BankAccount:
    def __init__(self, owner, balance):
        self.owner = owner
        self._balance = balance

    def deposit(self, amount):
        self._balance += amount

    def get_balance(self):
        return self._balance
```

使用：

```python
account = BankAccount("Tom", 1000)
account.deposit(200)
print(account.get_balance())
```

这能避免外部代码随意操作对象内部状态。

---

## 🧬 继承

继承（Inheritance）允许一个类复用另一个类的属性和方法。

```python
class Animal:
    def eat(self):
        print("正在吃东西")

class Dog(Animal):
    def bark(self):
        print("汪汪叫")
```

使用：

```python
dog = Dog()
dog.eat()
dog.bark()
```

`Dog` 继承了 `Animal`，所以它既能 `eat()`，也能 `bark()`。

---

## 🔄 方法重写

子类可以重写父类的方法，定义自己的行为。

```python
class Animal:
    def speak(self):
        print("动物发出声音")

class Cat(Animal):
    def speak(self):
        print("喵喵喵")
```

调用：

```python
cat = Cat()
cat.speak()
```

---

## 🔗 `super()` 的作用

当子类想在扩展功能的同时保留父类逻辑时，可以使用 `super()`。

```python
class Person:
    def __init__(self, name):
        self.name = name

class Student(Person):
    def __init__(self, name, grade):
        super().__init__(name)
        self.grade = grade
```

这样子类可以复用父类的初始化逻辑。

---

## 🎭 多态

多态（Polymorphism）指的是：同样的方法调用，作用在不同对象上，可以表现出不同结果。

```python
class Dog:
    def speak(self):
        print("汪汪")

class Cat:
    def speak(self):
        print("喵喵")

def animal_sound(animal):
    animal.speak()

animal_sound(Dog())
animal_sound(Cat())
```

这里只要对象拥有 `speak()` 方法，就可以传给 `animal_sound()`。这也是 Python 动态语言的一个特点。

---

## 🧪 类属性和实例属性

### 实例属性

每个对象各自拥有，互不影响。

```python
class Student:
    def __init__(self, name):
        self.name = name
```

### 类属性

属于整个类，所有对象共享。

```python
class Student:
    school = "No.1 School"

    def __init__(self, name):
        self.name = name
```

使用：

```python
s1 = Student("Tom")
s2 = Student("Alice")

print(s1.school)
print(s2.school)
```

---

## 🛠️ 实例方法、类方法和静态方法

### 1. 实例方法

最常见，默认第一个参数是 `self`。

```python
class User:
    def show(self):
        print("实例方法")
```

### 2. 类方法

使用 `@classmethod`，第一个参数通常是 `cls`。

```python
class User:
    count = 0

    @classmethod
    def show_count(cls):
        print(cls.count)
```

### 3. 静态方法

使用 `@staticmethod`，不依赖实例，也不依赖类状态。

```python
class MathTool:
    @staticmethod
    def add(a, b):
        return a + b
```

---

## 🌟 一个综合示例

```python
class Animal:
    def __init__(self, name):
        self.name = name

    def speak(self):
        print("动物发声")

class Dog(Animal):
    def speak(self):
        print(self.name, "说：汪汪")

class Cat(Animal):
    def speak(self):
        print(self.name, "说：喵喵")

def make_sound(animal):
    animal.speak()

dog = Dog("Buddy")
cat = Cat("Kitty")

make_sound(dog)
make_sound(cat)
```

这个例子里同时体现了：

- `class`：定义类
- `object`：创建对象
- `inheritance`：继承
- `override`：方法重写
- `polymorphism`：多态

---

## 📌 学习建议

学习 Python 面向对象时，建议按下面顺序掌握：

1. 先理解类和对象
2. 再掌握属性、方法和 `self`
3. 然后学习封装、继承和多态
4. 最后再理解类方法、静态方法和更复杂的设计方式

只要把这些基础打牢，后面学习 Django、FastAPI、爬虫框架和各种第三方库会容易很多。

---

## 📚 参考资源

- 📖 Python 官方文档：[docs.python.org](https://docs.python.org/3/)
- 🎓 Python Tutorial - Classes：[docs.python.org/tutorial/classes.html](https://docs.python.org/3/tutorial/classes.html)
- 💻 Python 数据模型：[docs.python.org/reference/datamodel.html](https://docs.python.org/3/reference/datamodel.html)
