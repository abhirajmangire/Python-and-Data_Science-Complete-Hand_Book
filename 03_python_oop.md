# Chapter 3 — Python Advanced: Object-Oriented Programming

[← Previous: Intermediate](02_python_intermediate.md) | [Back to Index](../README.md) | [Next: Advanced Internals →](04_python_advanced.md)

---

## 3.1 Classes & Objects

```python
class Student:
    school_name = "ABC School"       # class attribute (shared by all instances)

    def __init__(self, name, age):    # constructor
        self.name = name                # instance attribute
        self.age = age

    def greet(self):                   # instance method
        return f"Hi, I'm {self.name}"

s1 = Student("Alice", 20)
s1.greet()
```

🔑 **Interview Q: Class attribute vs instance attribute?**
Class attributes are shared across all instances (stored once); instance attributes belong to each object separately. Modifying a class attribute via the class affects all instances; modifying via an instance creates a new instance attribute shadowing the class one.

---

## 3.2 The Four Pillars of OOP

### 1. Encapsulation
Bundling data + methods, restricting direct access.
```python
class Account:
    def __init__(self, balance):
        self._balance = balance         # convention: "protected" (single underscore)
        self.__pin = 1234                 # "private" (double underscore → name mangling)

    def get_balance(self):
        return self._balance
```
💡 Python has no true private members — `__pin` becomes `_Account__pin` internally (**name mangling**), discouraging but not preventing access.

### 2. Inheritance
```python
class Animal:
    def __init__(self, name):
        self.name = name
    def speak(self):
        return "..."

class Dog(Animal):                    # single inheritance
    def speak(self):                    # method overriding
        return f"{self.name} says Woof!"

class Cat(Animal):
    def speak(self):
        return f"{self.name} says Meow!"
```

**Multiple inheritance & MRO (Method Resolution Order):**
```python
class A:
    def show(self): return "A"
class B:
    def show(self): return "B"
class C(A, B):                        # C looks in A first, then B
    pass

C().show()          # "A"
C.__mro__            # shows resolution order (uses C3 linearization)
```

### 3. Polymorphism
Same interface, different implementations.
```python
for animal in [Dog("Rex"), Cat("Tom")]:
    print(animal.speak())    # each object responds differently to same call
```

### 4. Abstraction
```python
from abc import ABC, abstractmethod

class Shape(ABC):
    @abstractmethod
    def area(self):
        pass                 # must be implemented by subclasses

class Circle(Shape):
    def __init__(self, r):
        self.r = r
    def area(self):
        return 3.14159 * self.r ** 2

# Shape()  ❌ TypeError — can't instantiate abstract class
```

---

## 3.3 `super()`

```python
class Employee:
    def __init__(self, name, salary):
        self.name = name
        self.salary = salary

class Manager(Employee):
    def __init__(self, name, salary, team_size):
        super().__init__(name, salary)      # calls parent constructor
        self.team_size = team_size
```

---

## 3.4 Magic / Dunder Methods

```python
class Vector:
    def __init__(self, x, y):
        self.x, self.y = x, y

    def __repr__(self):              # for developers (debugging)
        return f"Vector({self.x}, {self.y})"

    def __str__(self):                # for end users (print())
        return f"({self.x}, {self.y})"

    def __add__(self, other):          # operator overloading: v1 + v2
        return Vector(self.x + other.x, self.y + other.y)

    def __eq__(self, other):            # enables ==
        return self.x == other.x and self.y == other.y

    def __len__(self):                   # enables len(obj)
        return 2
```

| Method | Triggered by |
|--------|-------------|
| `__init__` | object creation |
| `__str__` | `print(obj)`, `str(obj)` |
| `__repr__` | `repr(obj)`, console echo |
| `__add__`, `__sub__`, `__mul__` | `+`, `-`, `*` |
| `__eq__`, `__lt__`, `__gt__` | `==`, `<`, `>` |
| `__len__` | `len(obj)` |
| `__getitem__` | `obj[i]` (indexing) |
| `__iter__`, `__next__` | iteration protocol (`for x in obj`) |
| `__call__` | calling object like a function `obj()` |

---

## 3.5 Class Methods, Static Methods, Properties

```python
class Circle:
    def __init__(self, radius):
        self._radius = radius

    @property                       # accessed like an attribute, computed on the fly
    def area(self):
        return 3.14159 * self._radius ** 2

    @staticmethod                    # doesn't need self or cls, utility function
    def is_valid_radius(r):
        return r > 0

    @classmethod                      # receives the class itself, not an instance
    def from_diameter(cls, diameter):
        return cls(diameter / 2)

c = Circle(5)
c.area                               # accessed WITHOUT parentheses due to @property
Circle.is_valid_radius(5)
c2 = Circle.from_diameter(10)         # alternate constructor
```

🔑 **Interview Q: `@staticmethod` vs `@classmethod`?**
`@staticmethod` takes no implicit first argument — behaves like a plain function namespaced in the class. `@classmethod` takes `cls` as its first argument and can access/modify class state or act as an alternate constructor.

---

## 3.6 Decorators

A decorator wraps a function to extend its behavior without modifying it.

```python
def timer(func):
    import time
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)
        print(f"{func.__name__} took {time.time()-start:.4f}s")
        return result
    return wrapper

@timer
def slow_function():
    import time
    time.sleep(1)

slow_function()     # automatically timed
```

---

## ✅ Chapter 3 Summary

- OOP's 4 pillars: **Encapsulation, Inheritance, Polymorphism, Abstraction**
- `super()` calls the parent class's method
- Magic methods (`__init__`, `__str__`, `__add__`, etc.) let objects integrate with Python's built-in syntax
- `@property`, `@staticmethod`, `@classmethod` control how methods behave and are accessed
- Decorators wrap functions to add reusable behavior (logging, timing, auth checks)

---

[← Previous: Intermediate](02_python_intermediate.md) | [Back to Index](../README.md) | [Next: Advanced Internals →](04_python_advanced.md)
