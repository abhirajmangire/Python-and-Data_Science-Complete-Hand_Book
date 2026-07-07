# Chapter 7 — Data Visualization

[← Previous: Pandas](06_pandas.md) | [Back to Index](../README.md) | [Next: Statistics →](08_statistics.md)

---

## 7.1 Matplotlib Fundamentals

```python
import matplotlib.pyplot as plt
```

### Figure vs Axes
- **Figure**: the entire canvas/window that holds everything
- **Axes**: an individual plot within the figure (a figure can have multiple axes/subplots)

```python
fig, ax = plt.subplots(figsize=(8, 5))
ax.plot(x, y)
ax.set_title("My Plot")
ax.set_xlabel("X axis")
ax.set_ylabel("Y axis")
plt.show()

fig, axes = plt.subplots(2, 2, figsize=(10, 8))    # grid of subplots
axes[0, 0].plot(x, y)
```

🔑 **Interview Q: Difference between `plt.plot()` and `ax.plot()`?**
`plt.plot()` uses Matplotlib's implicit, state-based interface (operates on the "current" figure/axes). `ax.plot()` uses the explicit object-oriented interface, giving direct control over a specific Axes object — recommended for anything beyond a single simple plot.

---

## 7.2 Plot Types & When to Use Each

| Chart | Use Case | Code |
|-------|----------|------|
| **Line Plot** | Trends over continuous/time data | `plt.plot(x, y)` |
| **Bar Chart** | Comparing categories | `plt.bar(categories, values)` |
| **Horizontal Bar** | Long category names | `plt.barh(categories, values)` |
| **Histogram** | Distribution of a single numeric variable | `plt.hist(data, bins=20)` |
| **Scatter Plot** | Relationship between two numeric variables | `plt.scatter(x, y)` |
| **Pie Chart** | Proportions of a whole (use sparingly, ≤5 categories) | `plt.pie(sizes, labels=labels)` |
| **Box Plot** | Distribution, quartiles, outliers | `plt.boxplot(data)` |
| **Heatmap** | Correlation matrix / 2D intensity data | `sns.heatmap(df.corr())` |

### Histogram — deep dive
```python
plt.hist(df["Age"], bins=20, edgecolor="black")
```
- Shows the **frequency distribution** of a single continuous variable.
- `bins` controls granularity — too few bins hides detail, too many introduces noise.
- Use to check **skewness**, modality (uni/bi-modal), and normality.

### Scatter Plot — deep dive
```python
plt.scatter(df["Age"], df["Income"], alpha=0.6)
```
- Reveals **relationship/correlation** between two continuous variables.
- Look for linear patterns, clusters, or outliers.

### Correlation Heatmap — deep dive
```python
import seaborn as sns
sns.heatmap(df.corr(numeric_only=True), annot=True, cmap="coolwarm")
```
- Visualizes the correlation matrix — values range from **-1** (perfect negative correlation) to **+1** (perfect positive correlation), 0 means no linear relationship.
- `annot=True` prints the numeric correlation value on each cell.

### Box Plot — deep dive
Displays five-number summary: minimum, Q1 (25th percentile), median (Q2), Q3 (75th percentile), maximum, plus **outliers** as individual points beyond 1.5×IQR from the quartiles.

```python
sns.boxplot(x="City", y="Age", data=df)   # compare distributions across categories
```

---

## 7.3 Seaborn — Statistical Visualization

Seaborn is built on Matplotlib but provides higher-level, more attractive statistical plots with less code.

```python
import seaborn as sns

sns.histplot(df["Age"], kde=True)            # histogram + density curve
sns.countplot(x="City", data=df)                # bar chart of category counts
sns.pairplot(df)                                   # scatter matrix of all numeric column pairs
sns.violinplot(x="City", y="Age", data=df)            # distribution shape + box plot combined
sns.regplot(x="Age", y="Income", data=df)               # scatter + regression line
```

💡 **Matplotlib vs Seaborn**: Matplotlib gives you full manual control (more code, more flexibility). Seaborn gives you statistically-aware defaults (less code, works great with DataFrames directly), and integrates cleanly with Pandas.

---

## 7.4 Styling & Customization

```python
plt.style.use("seaborn-v0_8")            # pre-built style theme
plt.figure(figsize=(10, 6))
plt.plot(x, y, color="blue", linestyle="--", marker="o", label="Trend")
plt.legend()
plt.grid(True, alpha=0.3)
plt.xticks(rotation=45)
plt.tight_layout()                           # prevents label cutoff
plt.savefig("chart.png", dpi=300, bbox_inches="tight")
```

---

## 7.5 Word Cloud (for NLP visualization)

```python
from wordcloud import WordCloud
wc = WordCloud(width=800, height=400, background_color="white").generate(text)
plt.imshow(wc, interpolation="bilinear")
plt.axis("off")
```
Used to visualize the most frequent words in text data — larger font = higher frequency.

---

## 7.6 Choosing the Right Chart — Quick Guide

- **One numeric variable** → Histogram, Box Plot
- **One categorical variable** → Bar Chart, Pie Chart (few categories only)
- **Two numeric variables** → Scatter Plot
- **Numeric + categorical** → Box Plot, Violin Plot
- **Time series** → Line Plot
- **Correlation between many variables** → Heatmap
- **Proportions of a whole** → Pie Chart (sparingly) or stacked Bar Chart

⚠️ **Common mistake:** Using pie charts for many categories (>5-6) — they become unreadable. Bar charts scale better.

---

## ✅ Chapter 7 Summary

- Figure = whole canvas, Axes = individual plot within it
- Histogram → distribution, Scatter → relationship, Box Plot → spread & outliers, Heatmap → correlation
- Seaborn = statistically-aware, less code; Matplotlib = full manual control
- Always label axes, add titles, and choose chart type based on the question being answered

---

[← Previous: Pandas](06_pandas.md) | [Back to Index](../README.md) | [Next: Statistics →](08_statistics.md)
