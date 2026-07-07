# Chapter 5 — NumPy Complete

[← Previous: Advanced Internals](04_python_advanced.md) | [Back to Index](../README.md) | [Next: Pandas →](06_pandas.md)

---

## 5.1 What is NumPy & Why Use It?

**NumPy** (Numerical Python) is the foundational library for numerical computing in Python, providing the `ndarray` (n-dimensional array) object.

**Why NumPy over plain Python lists?**
- Arrays are stored in **contiguous memory blocks** (unlike lists of pointers) → much faster
- Supports **vectorized operations** (no explicit loops needed)
- Operations are implemented in C under the hood → huge speed advantage
- Broadcasting allows operations between arrays of different shapes

```python
import numpy as np
```

🔑 **Interview Q: Why is NumPy faster than lists?**
NumPy arrays are homogeneous (single data type) and stored contiguously in memory, enabling vectorized C-level operations (SIMD) instead of Python's per-element loop overhead and type-checking.

---

## 5.2 Creating Arrays

```python
a = np.array([1, 2, 3])                  # 1D array
b = np.array([[1, 2], [3, 4]])            # 2D array
np.zeros((3, 3))                            # array of zeros
np.ones((2, 4))                              # array of ones
np.full((2, 2), 7)                            # filled with constant
np.eye(3)                                       # identity matrix
np.arange(0, 10, 2)                              # [0 2 4 6 8]
np.linspace(0, 1, 5)                              # 5 evenly spaced values between 0-1
np.random.rand(3, 3)                               # uniform random [0,1)
np.random.randn(3, 3)                               # standard normal distribution
np.random.randint(0, 10, size=(2,3))                 # random ints
```

---

## 5.3 Array Attributes

```python
a.shape        # dimensions, e.g. (3, 4)
a.ndim          # number of dimensions
a.size           # total number of elements
a.dtype           # data type (int64, float64, etc.)
a.itemsize          # bytes per element
a.reshape(4, 3)       # change shape (must preserve total size)
a.flatten()             # collapse to 1D (returns copy)
a.ravel()                # collapse to 1D (returns view when possible)
a.T                       # transpose
```

---

## 5.4 Indexing & Slicing

```python
a = np.array([10, 20, 30, 40, 50])
a[1]            # 20
a[1:4]           # [20 30 40]
a[::-1]           # reversed

m = np.array([[1,2,3],[4,5,6],[7,8,9]])
m[1, 2]              # row 1, col 2 → 6
m[:, 0]               # entire first column
m[0, :]                # entire first row
m[0:2, 1:3]              # sub-matrix slice

mask = m > 5
m[mask]                    # boolean indexing → [6 7 8 9]
m[m % 2 == 0]                # even elements
```

---

## 5.5 Broadcasting

Broadcasting lets NumPy perform operations on arrays of **different shapes** by "stretching" the smaller one, without copying data.

```python
a = np.array([1, 2, 3])
a + 10                        # [11 12 13]  — scalar broadcasts to each element

m = np.array([[1,2,3],[4,5,6]])
v = np.array([10, 20, 30])
m + v                          # v is broadcast across each row
```

**Broadcasting rules:** dimensions are compared from the right; they're compatible if equal, or one of them is 1.

🔑 **Interview Q: What is broadcasting and why does it matter?**
Broadcasting allows NumPy to perform element-wise operations on arrays of different shapes without explicit loops or memory-costly replication, making code both faster and more concise.

---

## 5.6 Vectorization

```python
# Slow way (Python loop):
result = [x**2 for x in range(1000000)]

# Fast way (vectorized):
arr = np.arange(1000000)
result = arr ** 2       # orders of magnitude faster
```

Vectorization replaces explicit loops with array-wide operations executed in compiled C code.

---

## 5.7 Mathematical & Statistical Operations

```python
np.sum(a), np.mean(a), np.median(a), np.std(a), np.var(a)
np.min(a), np.max(a), np.argmin(a), np.argmax(a)
np.sqrt(a), np.exp(a), np.log(a)
np.sort(a)
np.unique(a)                        # unique values
np.cumsum(a)                          # cumulative sum
np.corrcoef(a, b)                       # correlation coefficient

# Axis-wise operations on 2D arrays
m.sum(axis=0)         # column-wise sum
m.sum(axis=1)         # row-wise sum
```

---

## 5.8 Linear Algebra

```python
np.dot(a, b)                  # dot product / matrix multiplication
a @ b                            # matrix multiplication (preferred, Python 3.5+)
np.linalg.inv(m)                   # matrix inverse
np.linalg.det(m)                     # determinant
np.linalg.eig(m)                       # eigenvalues & eigenvectors
np.transpose(m)                          # transpose
```

---

## 5.9 Array Manipulation

```python
np.concatenate([a, b])                # join arrays
np.vstack([a, b])                        # stack vertically
np.hstack([a, b])                          # stack horizontally
np.split(a, 3)                                # split into 3 equal parts
a.copy()                                        # explicit deep copy (vs view/slice which shares memory)
```

⚠️ **Common mistake:** Slicing an array (`a[1:3]`) returns a **view**, not a copy — modifying the slice modifies the original array. Use `.copy()` when you need an independent array.

---

## ✅ Chapter 5 Summary

- NumPy arrays (`ndarray`) are faster than lists due to contiguous memory + vectorization
- Broadcasting enables operations across different shapes without explicit replication
- Boolean/fancy indexing enables powerful filtering (`arr[arr > 5]`)
- Vectorized operations replace slow Python loops
- Slicing returns views (shared memory); use `.copy()` for independence

---

[← Previous: Advanced Internals](04_python_advanced.md) | [Back to Index](../README.md) | [Next: Pandas →](06_pandas.md)
