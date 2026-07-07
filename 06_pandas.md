# Chapter 6 — Pandas Complete

[← Previous: NumPy](05_numpy.md) | [Back to Index](../README.md) | [Next: Visualization →](07_visualization.md)

---

## 6.1 What is Pandas & Why Use It?

**Pandas** is Python's core library for structured/tabular data manipulation and analysis, built on top of NumPy. It introduces two key data structures:

- **Series** — a 1D labeled array (like a single column)
- **DataFrame** — a 2D labeled table (like an Excel sheet or SQL table)

```python
import pandas as pd
```

🔑 **Interview Q: Series vs DataFrame?**
A Series is a single labeled 1D array (one column with an index). A DataFrame is a collection of Series sharing the same index — essentially a 2D table with labeled rows and columns.

---

## 6.2 Creating Series & DataFrames

```python
s = pd.Series([10, 20, 30], index=["a", "b", "c"])

df = pd.DataFrame({
    "Name": ["Alice", "Bob", "Cara"],
    "Age": [25, 30, 22],
    "City": ["NYC", "LA", "SF"]
})

df = pd.read_csv("data.csv")
df = pd.read_excel("data.xlsx")
df = pd.read_json("data.json")
```

---

## 6.3 Inspecting Data

| Function | What it Does | Returns |
|----------|--------------|---------|
| `df.head(n)` | First `n` rows (default 5) | DataFrame |
| `df.tail(n)` | Last `n` rows | DataFrame |
| `df.shape` | (rows, columns) | tuple |
| `df.info()` | dtypes, non-null counts, memory usage | None (prints) |
| `df.describe()` | count, mean, std, min, quartiles, max for numeric cols | DataFrame |
| `df.columns` | Column names | Index |
| `df.dtypes` | Data type of each column | Series |
| `df.isnull().sum()` | Count of missing values per column | Series |

### `df.head()` — deep explanation
- **What**: Returns the first `n` rows of the DataFrame.
- **Why**: Quick sanity check of data structure/content without printing the whole dataset.
- **Syntax**: `df.head(n=5)`
- **Parameters**: `n` (int, default 5) — number of rows.
- **Return type**: `pandas.DataFrame`
- **Common mistake**: Assuming `head()` modifies the DataFrame — it doesn't; it returns a **new view**, the original `df` is unchanged.

### `df.describe()` — deep explanation
- **What**: Generates summary statistics (count, mean, std, min, 25%, 50%, 75%, max) for numeric columns.
- **Why**: Fast way to understand distribution, spot outliers, check scale differences between features.
- **Parameters**: `include="all"` to also summarize categorical columns.
- 🔑 **Interview Q**: How do you interpret `std` being very large relative to `mean`? → High variability/dispersion, potential outliers.

---

## 6.4 Selecting Data

```python
df["Name"]                   # single column → Series
df[["Name", "Age"]]           # multiple columns → DataFrame

df.loc[0]                       # row by LABEL
df.iloc[0]                       # row by POSITION (integer index)
df.loc[0:2, "Name":"Age"]          # label-based slicing (inclusive of endpoint!)
df.iloc[0:2, 0:2]                    # position-based slicing (exclusive of endpoint)

df[df["Age"] > 25]                     # boolean filtering
df[(df["Age"] > 25) & (df["City"]=="NYC")]  # multiple conditions — use & | not and/or
```

🔑 **Interview Q: `.loc` vs `.iloc`?**
`.loc` selects by **label** (index name / column name), and label-based slicing is **inclusive** of the end label. `.iloc` selects by **integer position**, and slicing is **exclusive** of the end position, matching Python's normal slicing behavior.

---

## 6.5 Handling Missing Data

```python
df.isnull()                    # boolean mask of missing values
df.isnull().sum()                 # count of NaNs per column
df.dropna()                          # drop rows with ANY NaN
df.dropna(axis=1)                       # drop columns with any NaN
df.dropna(thresh=3)                        # keep rows with at least 3 non-NaN values
df.fillna(0)                                  # fill NaNs with 0
df.fillna(df.mean(numeric_only=True))            # fill with column mean
df["col"].fillna(df["col"].median())               # median (robust to outliers)
df.fillna(method="ffill")                             # forward fill
df.fillna(method="bfill")                               # backward fill
```

🔑 **Interview Q: Null vs NaN — are they the same?**
`None` is Python's native null object (object dtype). `NaN` (Not a Number, from NumPy/IEEE 754) is used for missing **numeric** data. Pandas treats both as "missing" via `isnull()`, but internally a column of `None` mixed with numbers often gets upcast to `NaN` for consistency.

💡 **Mean vs Median vs Mode for imputation:**
- **Mean**: use for normally distributed numeric data without extreme outliers
- **Median**: use when data is skewed or has outliers (robust to extremes)
- **Mode**: use for categorical data

---

## 6.6 Removing Duplicates & Data Cleaning

```python
df.duplicated()                  # boolean mask of duplicate rows
df.drop_duplicates()                # remove duplicate rows
df.drop_duplicates(subset=["Name"])    # based on specific column(s)
df.drop(columns=["City"])                 # drop a column
df.rename(columns={"Name": "FullName"})     # rename columns
df.astype({"Age": "float"})                    # change data type
df["Name"] = df["Name"].str.strip().str.title()  # clean text data
```

---

## 6.7 GroupBy — Split-Apply-Combine

```python
df.groupby("City")["Age"].mean()                  # average age per city
df.groupby("City").agg({"Age": "mean", "Name": "count"})
df.groupby(["City", "Gender"]).size()                # multi-level grouping
df.groupby("City").apply(lambda g: g["Age"].max() - g["Age"].min())
```

🔑 **Interview Q: Explain the "split-apply-combine" pattern.**
`groupby()` **splits** the data into groups based on a key, **applies** a function (aggregation, transformation) to each group independently, then **combines** the results back into a single output structure.

---

## 6.8 Merging, Joining, Concatenating

```python
pd.concat([df1, df2])                     # stack DataFrames (rows or columns via axis)
pd.merge(df1, df2, on="id", how="inner")     # SQL-style join
# how: "inner", "left", "right", "outer"
df1.join(df2, how="left")                       # join on index
```

| `how` | Behavior |
|-------|----------|
| `inner` | Only matching keys in both |
| `left` | All rows from left, matched from right |
| `right` | All rows from right, matched from left |
| `outer` | All rows from both, NaN where unmatched |

---

## 6.9 Value Counts & Frequency

```python
df["City"].value_counts()                 # frequency of each unique value
df["City"].value_counts(normalize=True)      # as proportions
df["City"].unique()                             # array of unique values
df["City"].nunique()                              # count of unique values
```

---

## 6.10 Correlation & Sorting

```python
df.corr(numeric_only=True)             # correlation matrix (Pearson by default)
df.sort_values("Age", ascending=False)    # sort rows by column
df.sort_index()                              # sort by index
```

---

## 6.11 Apply / Map / Applymap

```python
df["Age"].apply(lambda x: x + 1)                # element-wise on a Series
df.apply(lambda row: row["Age"] * 2, axis=1)       # row-wise on a DataFrame
df["City"].map({"NYC": "New York", "LA": "Los Angeles"})   # value mapping
df.applymap(lambda x: str(x).upper())                 # element-wise on entire DataFrame (deprecated in favor of df.map in newer pandas)
```

---

## 6.12 Common Errors Reference

| Error | Cause |
|-------|-------|
| `SettingWithCopyWarning` | Modifying a slice of a DataFrame instead of using `.loc[]` |
| `KeyError` | Column name typo or doesn't exist |
| `ValueError: cannot convert...` | Trying to cast incompatible data (e.g., text to int) |
| Shape mismatch on merge | Key columns have different dtypes (e.g., int vs str) |

💡 **Fix for `SettingWithCopyWarning`:**
```python
df.loc[df["Age"] > 25, "Category"] = "Adult"     # ✅ correct
# NOT: df[df["Age"]>25]["Category"] = "Adult"    # ❌ chained indexing warning
```

---

## ✅ Chapter 6 Summary

- Series = 1D labeled array; DataFrame = 2D labeled table
- `.loc` = label-based (inclusive slicing), `.iloc` = position-based (exclusive slicing)
- Handle missing data with `isnull()`, `dropna()`, `fillna()` — choose mean/median/mode based on distribution
- `groupby()` implements split-apply-combine
- `merge()`/`concat()`/`join()` combine DataFrames like SQL joins

---

[← Previous: NumPy](05_numpy.md) | [Back to Index](../README.md) | [Next: Visualization →](07_visualization.md)
