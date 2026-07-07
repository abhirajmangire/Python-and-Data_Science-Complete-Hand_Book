# Chapter 4 — Python Advanced: Internals

[← Previous: OOP](03_python_oop.md) | [Back to Index](../README.md) | [Next: NumPy →](05_numpy.md)

---

## 4.1 Iterators & Iterables

An **iterable** is anything you can loop over (`__iter__` method). An **iterator** is an object with `__next__`, produced by calling `iter()` on an iterable.

```python
lst = [1, 2, 3]
it = iter(lst)          # creates an iterator
next(it)                  # 1
next(it)                  # 2
next(it)                  # 3
next(it)                  # ❌ StopIteration
```

**Custom iterator:**
```python
class Counter:
    def __init__(self, limit):
        self.limit = limit
        self.n = 0
    def __iter__(self):
        return self
    def __next__(self):
        if self.n >= self.limit:
            raise StopIteration
        self.n += 1
        return self.n

for num in Counter(5):
    print(num)     # 1 2 3 4 5
```

---

## 4.2 Generators

Generators use `yield` to produce values lazily — one at a time — without storing the whole sequence in memory.

```python
def count_up(limit):
    n = 1
    while n <= limit:
        yield n
        n += 1

for num in count_up(5):
    print(num)

gen = count_up(3)
next(gen)     # 1 — execution pauses at yield, resumes on next call
```

**Generator expression** (like list comprehension but lazy):
```python
squares = (x**2 for x in range(1000000))   # doesn't compute all values upfront
```

🔑 **Interview Q: Generator vs List — why use one over the other?**
Generators are **memory-efficient** — they yield one value at a time instead of holding the entire sequence in memory. Ideal for large or infinite sequences, streaming data, or file processing. Lists allow random access, repeated iteration, and slicing — generators are single-use and forward-only.

---

## 4.3 Closures

A closure is a function that "remembers" variables from its enclosing scope even after that scope has finished executing.

```python
def make_multiplier(factor):
    def multiplier(x):
        return x * factor      # 'factor' is captured from the enclosing scope
    return multiplier

double = make_multiplier(2)
double(5)      # 10
```

---

## 4.4 `*args`, `**kwargs`, and Unpacking

```python
def func(*args, **kwargs):
    print(args)      # tuple
    print(kwargs)     # dict

nums = [1, 2, 3]
func(*nums)            # unpacks list into positional args

d = {"a": 1, "b": 2}
func(**d)               # unpacks dict into keyword args

a, *rest = [1, 2, 3, 4]   # a=1, rest=[2,3,4]
```

---

## 4.5 Context Managers

The `with` statement relies on the **context manager protocol** (`__enter__` / `__exit__`).

```python
class FileManager:
    def __init__(self, filename, mode):
        self.filename, self.mode = filename, mode
    def __enter__(self):
        self.file = open(self.filename, self.mode)
        return self.file
    def __exit__(self, exc_type, exc_val, exc_tb):
        self.file.close()

with FileManager("test.txt", "w") as f:
    f.write("hello")
```

💡 Simpler alternative using `contextlib`:
```python
from contextlib import contextmanager

@contextmanager
def open_file(name, mode):
    f = open(name, mode)
    try:
        yield f
    finally:
        f.close()
```

---

## 4.6 Memory Model: Mutability & References

```python
def modify_list(lst):
    lst.append(4)      # mutates the original object (passed by reference)

def reassign(lst):
    lst = [100]          # rebinds local name only, doesn't affect caller

a = [1, 2, 3]
modify_list(a)   # a is now [1, 2, 3, 4]
reassign(a)       # a is still [1, 2, 3, 4] — unaffected
```

🔑 **Interview Q: Is Python "pass by value" or "pass by reference"?**
Python is **"pass by object reference"** (sometimes called "pass by assignment"). The variable name is bound to the object; mutable objects can be changed in place through that reference, but reassigning the parameter inside the function does not affect the caller's variable.

### Shallow vs Deep Copy
```python
import copy
a = [[1, 2], [3, 4]]
b = a.copy()               # shallow copy — inner lists still shared!
b[0][0] = 99                 # also changes a[0][0]!

c = copy.deepcopy(a)          # fully independent copy
```

---

## 4.7 Multithreading & Multiprocessing

```python
import threading
def task():
    print("running")

t = threading.Thread(target=task)
t.start()
t.join()

from multiprocessing import Process
p = Process(target=task)
p.start()
p.join()
```

🔑 **Interview Q: What is the GIL (Global Interpreter Lock)?**
The GIL is a mutex in CPython that allows only **one thread** to execute Python bytecode at a time, even on multi-core machines. This means threading doesn't give true parallelism for CPU-bound tasks (only for I/O-bound tasks like network calls). For CPU-bound parallelism, use **multiprocessing** instead, which runs separate processes each with their own interpreter and memory space.

---

## 4.8 Common Errors Reference

| Error | Typical Cause |
|-------|---------------|
| `ModuleNotFoundError` | Package not installed / wrong environment active |
| `IndentationError` | Mixed tabs/spaces, missing indent after `:` |
| `TypeError` | Wrong type used in operation (e.g., `"2" + 2`) |
| `ValueError` | Right type, invalid value (e.g., `int("abc")`) |
| `KeyError` | Accessing a missing dict key |
| `IndexError` | List index out of range |
| `AttributeError` | Calling a method/attribute that doesn't exist on that object |
| `NameError` | Using a variable before defining it |
| `ZeroDivisionError` | Division by zero |

---

## ✅ Chapter 4 Summary

- Iterators implement `__iter__`/`__next__`; generators (`yield`) produce lazily, saving memory
- Closures capture enclosing-scope variables
- `with` statements rely on `__enter__`/`__exit__` context manager protocol
- Python passes object references — mutable objects can be changed in-place inside functions
- The GIL limits true multi-core parallelism for threads; use multiprocessing for CPU-bound work

---

[← Previous: OOP](03_python_oop.md) | [Back to Index](../README.md) | [Next: NumPy →](05_numpy.md)
