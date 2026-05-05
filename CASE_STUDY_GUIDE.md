# Case Study Build Guide

This guide documents how the financial planning case study was built so the same process can be reused for future projects.

## Project Files

The final project uses these main files:

| File | Purpose |
| --- | --- |
| `widow_financial_planning_case_study.ipynb` | Main Jupyter Notebook case study |
| `widow_financial_planning_case_study.py` | Python script version exported from the notebook |
| `README.md` | GitHub project overview |
| `CASE_STUDY_GUIDE.md` | Reusable guide for rebuilding or practicing this type of project |

## Recommended Kernel

Use this Anaconda Jupyter kernel:

```text
anaconda-2025.12-py3.13
```

This kernel was confirmed to have the needed packages:

- pandas
- numpy
- matplotlib
- streamlit

## Case Study Structure

A strong beginner-to-intermediate data case study should follow this order:

1. Title and introduction
2. Author perspective or project motivation
3. Case study question
4. Dataset description
5. Data creation or data loading
6. Data preview
7. Summary statistics
8. Main analysis section 1
9. Main analysis section 2
10. Main analysis section 3
11. Findings
12. Recommendations
13. Limitations
14. Conclusion

For this project, the analysis sections were:

1. Advisor analysis
2. Income loss analysis
3. Insurance payout analysis
4. Net worth analysis

## Markdown vs Code Cells

Use Markdown cells for writing explanations:

```markdown
## Case Study Question

How does financial advisor support relate to financial stress after widowhood?
```

Use Code cells for Python:

```python
import pandas as pd
import numpy as np
```

Do not type the Jupyter prompt into cells:

```text
[ ]:
```

That is only part of the notebook interface.

## Step 1: Title And Introduction

Cell type: Markdown

```markdown
# Financial Planning After Widowhood

## A Python Case Study on Income Loss, Insurance Payouts, Advisor Support, and Financial Stress

Widowhood can create sudden financial complexity at the same time a person is navigating grief and major life changes. Income may shift, household expenses may need to be re-evaluated, insurance decisions can become urgent, and long-term planning may feel overwhelming.

This case study uses Python to explore how income loss, savings, insurance payouts, and financial advisor support may relate to financial stress after widowhood. The dataset is simulated for learning and portfolio purposes and does not represent real individuals.
```

## Step 2: Author Perspective

Cell type: Markdown

```markdown
## Author's Perspective

This project is informed by my professional background as a financial advisor for more than 20 years and by my personal experience of being widowed in my 40s. That perspective shaped the topic, but the analysis remains intentionally data-focused and uses simulated data only.

This case study is for educational and portfolio purposes. It is not financial advice and should not be interpreted as a recommendation for any specific individual.
```

## Step 3: Case Study Question

Cell type: Markdown

```markdown
## Case Study Question

How do income loss, savings, insurance payouts, and financial advisor support relate to financial stress after widowhood?

This question is designed to connect financial data analysis with practical planning concerns, including cash flow stability, risk management, and the role of professional guidance during a major life transition.
```

## Step 4: Dataset Description

Cell type: Markdown

```markdown
## Dataset Description

This case study uses a simulated dataset of 200 widowed individuals. The dataset includes age, income before and after loss, savings, financial advisor status, time since loss, expenses after loss, insurance payout, current net worth, income loss, and financial stress score.

The financial stress score is measured on a scale where higher values represent higher financial stress. Because the dataset is simulated, the purpose is not to prove a real-world relationship. The purpose is to demonstrate how Python can support financial planning analysis and help turn financial information into structured recommendations.
```

## Step 5: Create The Dataset

Cell type: Code

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

# Set seed so the simulated data is repeatable
np.random.seed(42)

# Number of simulated individuals
n = 200

# Create simulated dataset
df = pd.DataFrame({
    "age": np.random.randint(40, 80, n),
    "income_pre": np.random.randint(40000, 120000, n),
    "savings_pre": np.random.randint(50000, 500000, n),
    "has_advisor": np.random.choice(["Yes", "No"], n),
    "time_since_loss_years": np.round(np.random.uniform(0.5, 10, n), 1)
})

# Income after loss drops between 20% and 50%
df["income_post"] = df["income_pre"] * np.random.uniform(0.5, 0.8, n)

# Expenses are reduced, but not as much as income
df["expenses_post"] = df["income_post"] * np.random.uniform(0.7, 0.95, n)

# Some individuals receive insurance payouts
df["insurance_payout"] = np.random.choice([0, 50000, 100000, 200000], n)

# Current net worth after expenses over time
df["net_worth_current"] = (
    df["savings_pre"]
    + df["insurance_payout"]
    - (df["expenses_post"] * df["time_since_loss_years"])
)

# Income loss
df["income_loss"] = df["income_pre"] - df["income_post"]

# Financial stress score, where higher means more stress
df["financial_stress_score"] = np.round(
    (df["income_loss"] / df["income_pre"]) * 10
    + np.random.uniform(0, 2, n),
    1
)

# Prevent negative net worth values
df["net_worth_current"] = df["net_worth_current"].clip(lower=0)

df.head()
```

## Step 6: Summary Statistics

Cell type: Code

```python
summary_columns = [
    "age",
    "income_pre",
    "income_post",
    "income_loss",
    "savings_pre",
    "expenses_post",
    "insurance_payout",
    "net_worth_current",
    "financial_stress_score"
]

df[summary_columns].describe().round(2)
```

## Step 7: Advisor Analysis

Cell type: Code

```python
advisor_summary = df.groupby("has_advisor").agg(
    average_stress=("financial_stress_score", "mean"),
    average_income_loss=("income_loss", "mean"),
    average_net_worth=("net_worth_current", "mean"),
    count=("has_advisor", "count")
).round(2)

advisor_summary
```

Cell type: Code

```python
advisor_summary["average_stress"].plot(
    kind="bar",
    color=["#d95f02", "#1b9e77"]
)

plt.title("Average Financial Stress by Advisor Status")
plt.xlabel("Has Financial Advisor")
plt.ylabel("Average Stress Score")
plt.xticks(rotation=0)
plt.show()
```

## Step 8: Income Loss Analysis

Cell type: Code

```python
df.plot.scatter(
    x="income_loss",
    y="financial_stress_score",
    alpha=0.65,
    color="#386cb0"
)

plt.title("Income Loss vs Financial Stress")
plt.xlabel("Income Loss")
plt.ylabel("Financial Stress Score")
plt.show()

income_stress_correlation = df["income_loss"].corr(df["financial_stress_score"])
print("Correlation between income loss and financial stress:", round(income_stress_correlation, 3))
```

## Step 9: Insurance Payout Analysis

Cell type: Code

```python
insurance_summary = df.groupby("insurance_payout").agg(
    average_stress=("financial_stress_score", "mean"),
    average_net_worth=("net_worth_current", "mean"),
    count=("insurance_payout", "count")
).round(2)

insurance_summary
```

Cell type: Code

```python
insurance_summary["average_stress"].plot(
    kind="bar",
    color="#7570b3"
)

plt.title("Average Financial Stress by Insurance Payout")
plt.xlabel("Insurance Payout")
plt.ylabel("Average Stress Score")
plt.xticks(rotation=45)
plt.show()
```

## Step 10: Net Worth Analysis

Cell type: Code

```python
net_worth_correlations = df[
    [
        "age",
        "income_pre",
        "income_post",
        "income_loss",
        "savings_pre",
        "expenses_post",
        "insurance_payout",
        "time_since_loss_years",
        "financial_stress_score",
        "net_worth_current"
    ]
].corr(numeric_only=True)["net_worth_current"].sort_values(ascending=False)

net_worth_correlations.round(3)
```

Cell type: Code

```python
net_worth_correlations.drop("net_worth_current").plot(
    kind="barh",
    color="#4daf4a"
)

plt.title("Correlation With Current Net Worth")
plt.xlabel("Correlation")
plt.ylabel("Financial Factor")
plt.show()
```

## Step 11: Findings, Recommendations, Limitations, And Conclusion

Use Markdown cells to explain what the analysis means. Strong case studies do not stop at charts. They interpret the results and connect them to practical decisions.

Recommended final writing sections:

```markdown
## Key Findings
```

```markdown
## Recommendations
```

```markdown
## Limitations
```

```markdown
## Conclusion
```

## Quality Checklist

Before uploading to GitHub, confirm:

- The notebook runs from top to bottom without errors.
- The title is clear and specific.
- The dataset is described honestly as simulated.
- Each chart has a clear title and axis labels.
- Findings do not overclaim causation.
- Recommendations connect to the analysis.
- Limitations are included.
- The README matches the notebook title and file names.

## GitHub Upload Checklist

Upload these files:

```text
widow_financial_planning_case_study.ipynb
widow_financial_planning_case_study.py
README.md
CASE_STUDY_GUIDE.md
```

Optional but not required:

```text
*.bak backup files
```

Do not upload backup files unless you specifically want them visible in the repository.
