# Chapter 1 — Python Basics

[← Back to Index](README.md) | [Next: Python Intermediate →](02_python_intermediate.md)

---

## 1.1 What is Python?

Python is a **high-level, interpreted, general-purpose programming language** created by Guido van Rossum, first released in 1991. It emphasizes code readability with significant use of whitespace (indentation) instead of braces.

**Why Python is popular:**
- Simple, readable syntax (close to English)
- Interpreted — no compilation step needed
- Dynamically typed — no need to declare variable types
- Huge standard library + third-party ecosystem (pip)
- Multi-paradigm: supports procedural, object-oriented, and functional programming
- Dominant language in Data Science, ML, AI, automation, web backend (Django/Flask)

🔑 **Interview Q: Is Python compiled or interpreted?**
Python source code is first compiled to **bytecode** (`.pyc`), which is then executed by the **Python Virtual Machine (PVM)**. So it's technically both — but from a user's perspective, it behaves as an interpreted language since there's no separate manual compile step.

---

## 1.2 Installing & Running Python

```bash
python --version        # check version
python3 script.py       # run a script
python                  # open interactive REPL
```

💡 Use virtual environments to isolate project dependencies:
```bash
python -m venv myenv
source myenv/bin/activate      # Linux/Mac
myenv\Scripts\activate         # Windows
pip install pandas numpy
```

---

## 1.3 Variables

A variable is a name bound to a value stored in memory. Python is **dynamically typed** — you don't declare a type; it's inferred at runtime.

```python
x = 10          # int
name = "Alice"  # str
price = 99.99   # float
is_valid = True # bool
```

**Rules for variable names:**
- Must start with a letter or underscore (not a digit)
- Can contain letters, digits, underscores
- Case-sensitive (`age` and `Age` are different)
- Cannot be a reserved keyword (`if`, `for`, `class`, etc.)

**Multiple assignment:**
```python
a, b, c = 1, 2, 3
x = y = z = 0          # all point to same value
a, b = b, a             # swap without a temp variable
```

⚠️ **Common mistake:** Confusing `=` (assignment) with `==` (equality comparison).

---

## 1.4 Data Types

| Type | Example | Mutable? |
|------|---------|----------|
| `int` | `42` | No |
| `float` | `3.14` | No |
| `complex` | `2+3j` | No |
| `str` | `"hello"` | No |
| `bool` | `True`, `False` | No |
| `list` | `[1, 2, 3]` | **Yes** |
| `tuple` | `(1, 2, 3)` | No |
| `set` | `{1, 2, 3}` | **Yes** |
| `dict` | `{"a": 1}` | **Yes** |
| `NoneType` | `None` | No |

```python
type(x)          # returns the type of x
isinstance(x, int)  # checks if x is of type int
```

🔑 **Interview Q: Difference between mutable and immutable types?**
Mutable objects (list, dict, set) can be changed in place after creation — their identity (`id()`) stays the same. Immutable objects (int, str, tuple) cannot be changed; any "modification" creates a new object in memory.

```python
s = "hello"
s[0] = "H"     # ❌ TypeError — strings are immutable
lst = [1,2,3]
lst[0] = 99    # ✅ works — lists are mutable
```

---

## 1.5 Type Conversion (Casting)

```python
int("10")        # 10
float("3.14")    # 3.14
str(100)         # "100"
list("abc")      # ['a', 'b', 'c']
bool(0)           # False
bool("")          # False  (empty string is falsy)
bool("False")     # True   ⚠️ non-empty string is always truthy!
```

⚠️ **Common mistake:** `bool("False")` returns `True` because it's a non-empty string — Python checks truthiness, not the text content.

---

## 1.6 Operators

### Arithmetic
```python
+   -   *   /    # add, sub, mul, true division (always float)
//              # floor division
%               # modulus (remainder)
**              # exponentiation
```

```python
7 / 2    # 3.5
7 // 2   # 3
7 % 2    # 1
2 ** 10  # 1024
```

### Comparison
`==  !=  >  <  >=  <=` → all return `bool`

### Logical
`and  or  not` — Python uses **short-circuit evaluation**:
```python
x = 0 or "default"   # "default" (0 is falsy)
y = None and 5        # None (short circuits, never evaluates 5)
```

### Identity vs Equality
```python
a = [1, 2]
b = [1, 2]
a == b   # True  → same VALUE
a is b   # False → different OBJECT in memory
a is a   # True
```

🔑 **Interview Q: `==` vs `is`?**
`==` compares **values**. `is` compares **object identity** (memory address, via `id()`). Always use `is` only for `None`, `True`, `False` comparisons (`if x is None:`), never for value equality.

### Bitwise
```python
&   |   ^   ~   <<   >>
```

---

## 1.7 Input & Output

```python
name = input("Enter your name: ")   # always returns a string
age = int(input("Enter age: "))     # must cast manually

print("Hello", name, sep=", ", end="!\n")
print(f"You are {age} years old")   # f-string (preferred, Python 3.6+)
print("{} is {}".format(name, age)) # .format() method
print("%s is %d" % (name, age))     # old-style (legacy, avoid in new code)
```

💡 f-strings support expressions and formatting directly:
```python
pi = 3.14159
print(f"{pi:.2f}")        # 3.14
print(f"{1000000:,}")     # 1,000,000
print(f"{name!r}")        # repr() version, wraps in quotes
```

---

## 1.8 Control Flow

### if / elif / else
```python
if age < 13:
    category = "child"
elif age < 20:
    category = "teen"
else:
    category = "adult"
```

Python has no `switch` statement (pre-3.10). Python 3.10+ introduced **structural pattern matching**:
```python
match command:
    case "start":
        print("Starting")
    case "stop":
        print("Stopping")
    case _:
        print("Unknown command")   # default case
```

### Loops

```python
for i in range(5):        # 0 1 2 3 4
    print(i)

for i in range(2, 10, 2):  # start, stop, step → 2 4 6 8
    print(i)

while count < 5:
    count += 1

for i in range(10):
    if i == 3:
        continue    # skip this iteration
    if i == 7:
        break        # exit loop entirely
    print(i)
else:
    print("Loop completed without break")   # runs only if no `break` occurred
```

🔑 **Interview Q: What does the `else` clause on a `for`/`while` loop do?**
It executes only if the loop completes **without** hitting a `break`. Rarely used but a classic interview trick question.

---

## 1.9 Indentation Rules

Python uses indentation (4 spaces is PEP 8 standard) instead of `{}` to define code blocks. Mixing tabs and spaces causes `IndentationError` / `TabError`.

```python
if True:
    print("correctly indented")
      print("this will cause an IndentationError")
```

---

## 1.10 Comments & Docstrings

```python
# single line comment

"""
Multi-line string,
often used as a docstring
"""

def add(a, b):
    """Returns the sum of a and b."""   # function docstring, accessible via add.__doc__
    return a + b
```

---

## ✅ Chapter 1 Summary

- Python is dynamically typed, interpreted (via bytecode + PVM)
- Core types: int, float, str, bool, list, tuple, set, dict, None
- Mutable: list, set, dict | Immutable: int, str, tuple, float
- `==` for value equality, `is` for identity
- Indentation defines blocks — no braces
- f-strings are the modern way to format output

---

[← Back to Index](../README.md) | [Next: Python Intermediate →](02_python_intermediate.md)
