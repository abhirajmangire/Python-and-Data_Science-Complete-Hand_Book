# Chapter 2 — Python Intermediate

[← Previous: Basics](01_python_basics.md) | [Back to Index](../README.md) | [Next: OOP →](03_python_oop.md)

---

## 2.1 Functions

```python
def greet(name, greeting="Hello"):     # default parameter
    return f"{greeting}, {name}!"

greet("Alice")                # "Hello, Alice!"
greet("Bob", "Hi")             # "Hi, Bob!"
greet(name="Cara", greeting="Hey")   # keyword arguments
```

### `*args` and `**kwargs`
```python
def total(*args):                 # collects positional args into a tuple
    return sum(args)

def show(**kwargs):                # collects keyword args into a dict
    for k, v in kwargs.items():
        print(k, v)

total(1, 2, 3)                     # 6
show(name="Alice", age=25)
```

🔑 **Interview Q: Order of parameters?**
`def f(a, b, *args, c=1, **kwargs):` — positional → `*args` → keyword-only → `**kwargs`.

### Lambda (anonymous functions)
```python
square = lambda x: x ** 2
sorted(data, key=lambda x: x[1])   # commonly used with sort/filter/map
```

### `map`, `filter`, `reduce`
```python
list(map(lambda x: x*2, [1,2,3]))          # [2, 4, 6]
list(filter(lambda x: x % 2 == 0, [1,2,3,4]))  # [2, 4]

from functools import reduce
reduce(lambda a, b: a + b, [1,2,3,4])       # 10
```

### Scope: Local, Enclosing, Global, Built-in (LEGB)
```python
x = "global"
def outer():
    x = "enclosing"
    def inner():
        x = "local"
        print(x)     # "local"
    inner()
    print(x)          # "enclosing"
print(x)               # "global"
```

Use `global` / `nonlocal` to modify outer-scope variables from inside a function:
```python
count = 0
def increment():
    global count
    count += 1
```

---

## 2.2 Strings (Deep Dive)

Strings are **immutable sequences** of Unicode characters.

```python
s = "Data Science"

s.lower(), s.upper()            # case conversion
s.strip()                        # remove leading/trailing whitespace
s.split(" ")                     # ['Data', 'Science']
"-".join(["a","b","c"])          # "a-b-c"
s.replace("Data", "Python")      # "Python Science"
s.find("Science")                # index, or -1 if not found
s.startswith("Data")             # True
s.endswith("nce")                # True
s[0:4]                            # slicing → "Data"
s[::-1]                           # reverse string
len(s)                             # length
s.isdigit(), s.isalpha(), s.isalnum()
```

### Slicing syntax: `s[start:stop:step]`
```python
s = "abcdefgh"
s[2:5]     # "cde"
s[:3]      # "abc"
s[3:]      # "defgh"
s[::2]     # "aceg"
s[::-1]    # "hgfedcba"
```

💡 String formatting recap: f-strings > `.format()` > `%` (legacy).

---

## 2.3 Collections Deep Dive

### List
```python
lst = [1, 2, 3]
lst.append(4)          # add to end
lst.insert(0, 0)        # insert at index
lst.remove(2)            # remove first matching value
lst.pop()                 # remove & return last item (or by index)
lst.sort()                 # in-place sort
sorted(lst)                 # returns new sorted list
lst.reverse()
len(lst)
2 in lst                     # membership check
```

**List comprehension** (Pythonic, faster than loops):
```python
squares = [x**2 for x in range(10)]
evens = [x for x in range(20) if x % 2 == 0]
matrix_flat = [x for row in matrix for x in row]   # nested comprehension
```

### Tuple — immutable, ordered
```python
t = (1, 2, 3)
t = 1, 2, 3           # parentheses optional
a, b, c = t             # unpacking
```
💡 Tuples are used for fixed collections and as **dictionary keys** (lists cannot be dict keys since they're mutable/unhashable).

### Set — unordered, unique elements
```python
s = {1, 2, 3}
s.add(4)
s.union({4,5})           # or s | {4,5}
s.intersection({2,3,9})   # or s & {2,3,9}
s.difference({2})          # or s - {2}
```
💡 Sets are ideal for deduplication and fast membership testing (`O(1)` average vs `O(n)` for lists).

### Dictionary — key-value pairs
```python
d = {"name": "Alice", "age": 25}
d["age"]              # 25
d.get("city", "N/A")   # safe access with default
d.keys(), d.values(), d.items()
d.update({"city": "NYC"})
del d["age"]

{k: v*2 for k, v in d.items()}   # dict comprehension
```

🔑 **Interview Q: List vs Tuple vs Set vs Dict — when to use which?**
- **List**: ordered, mutable, allows duplicates → general-purpose sequences
- **Tuple**: ordered, immutable → fixed records, dict keys, function returns
- **Set**: unordered, unique elements → deduplication, membership tests
- **Dict**: key-value mapping → fast lookups by key (`O(1)` average)

---

## 2.4 File Handling

```python
with open("data.txt", "r") as f:     # 'with' auto-closes the file
    content = f.read()                 # entire file as string
    lines = f.readlines()               # list of lines

with open("out.txt", "w") as f:        # 'w' overwrites, 'a' appends
    f.write("Hello\n")

with open("data.csv", "r") as f:
    for line in f:                       # memory-efficient line iteration
        print(line.strip())
```

⚠️ **Common mistake:** Forgetting `with` and manually calling `f.close()` — if an exception occurs before `close()`, the file handle leaks. `with` guarantees closure.

---

## 2.5 Exception Handling

```python
try:
    result = 10 / 0
except ZeroDivisionError as e:
    print(f"Error: {e}")
except (TypeError, ValueError) as e:    # catch multiple types
    print(f"Error: {e}")
else:
    print("Runs if no exception occurred")
finally:
    print("Always runs, exception or not")
```

### Raising custom exceptions
```python
class InsufficientFundsError(Exception):
    pass

def withdraw(balance, amount):
    if amount > balance:
        raise InsufficientFundsError("Not enough balance")
    return balance - amount
```

🔑 **Interview Q: `except Exception` vs bare `except`?**
Bare `except:` catches **everything**, including `KeyboardInterrupt` and `SystemExit`, which can hide bugs and prevent clean shutdowns. Always catch specific exceptions or at least `except Exception:`.

---

## 2.6 Modules & Packages

```python
import math
from math import sqrt, pi
import numpy as np              # aliasing (standard convention)
from mypackage.utils import helper_fn
```

- A **module** is a single `.py` file.
- A **package** is a folder containing an `__init__.py` (marks it importable) plus modules.

```python
if __name__ == "__main__":
    main()
```
💡 This guard ensures code only runs when the script is executed directly, not when imported as a module.

---

## ✅ Chapter 2 Summary

- Functions support default args, `*args`, `**kwargs`, lambdas
- Strings are immutable; slicing syntax `[start:stop:step]`
- List (mutable/ordered), Tuple (immutable/ordered), Set (unique/unordered), Dict (key-value)
- `with` statement for safe file handling
- `try/except/else/finally` for robust error handling

---

[← Previous: Basics](01_python_basics.md) | [Back to Index](../README.md) | [Next: OOP →](03_python_oop.md)
