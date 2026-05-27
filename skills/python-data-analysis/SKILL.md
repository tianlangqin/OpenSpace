---
name: python-data-analysis
description: Analyze data using Python. Create scripts that read, transform, summarize, and visualize datasets using pandas, numpy, and matplotlib.
---

# Python Data Analysis

Create Python scripts that analyze data — reading from CSV/JSON, computing statistics, and producing summaries or charts.

## Pattern

```python
import pandas as pd
import numpy as np

# Load data
df = pd.read_csv("data.csv")  # or pd.read_json, pd.read_excel

# Inspect
print(df.shape)
print(df.describe())

# Transform
df["new_col"] = df["col_a"] / df["col_b"]
grouped = df.groupby("category").agg({"value": ["mean", "sum", "count"]})

# Save results
grouped.to_csv("summary.csv")
print(grouped.to_string())
```

## Visualization

```python
import matplotlib
matplotlib.use("Agg")  # Non-interactive backend
import matplotlib.pyplot as plt

fig, ax = plt.subplots(figsize=(10, 6))
df.plot(kind="bar", x="category", y="value", ax=ax)
ax.set_title("Analysis Results")
plt.tight_layout()
plt.savefig("chart.png", dpi=150)
print("Chart saved to chart.png")
```

## Best Practices

- **Always verify by running**: After writing a script, execute it with `python3 <script>.py` and confirm output is correct before reporting results.
- **If execution fails, fix it**: Diagnose errors, apply fixes, and re-run. Install missing packages with `python3 -m pip install <package>`.
- **Never hallucinate output**: Do not state specific numerical results unless you observed them in actual execution output.
- Always use `matplotlib.use("Agg")` before importing pyplot (no display server needed)
- Print summary statistics to stdout for verification
- Handle missing data: `df.dropna()` or `df.fillna(0)`
- Use `python3 -m pip install pandas matplotlib numpy` if packages missing
- Save outputs to files (CSV, PNG) for reproducibility
