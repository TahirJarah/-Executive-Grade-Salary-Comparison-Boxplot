# -Executive-Grade-Salary-Comparison-Boxplot
I performed Exploratory Data Analysis (EDA) on U.S. data roles, comparing salary distributions for Data Analysts, Data Engineers, and Data Scientists using Python. I visualized compensation ranges with boxplots and highlighted median, mean, outliers, and sample size to communicate market differences clearly.

Key Insights:
• Data Engineers and Data Scientists show higher median salaries than Data Analysts.
• Mean salaries exceed medians across roles, indicating right-skewed distributions.
• Outliers suggest high-paying senior or specialized positions, especially for Data Scientists.

import matplotlib.pyplot as plt
from matplotlib.ticker import FuncFormatter
import numpy as np

# ---------------------------
# Filter roles in the US
# ---------------------------
job_titles = ['Data Analyst', 'Data Engineer', 'Data Scientist']

df_roles = df[
    (df['job_title_short'].isin(job_titles)) &
    (df['job_country'] == 'United States')
].copy()

# Remove missing salaries
df_roles.dropna(subset=['salary_year_avg'], inplace=True)

# Prepare data
job_data = [
    df_roles[df_roles['job_title_short'] == job]['salary_year_avg']
    for job in job_titles
]

# Compute statistics
medians = [np.median(salaries) for salaries in job_data]
means = [np.mean(salaries) for salaries in job_data]
counts = [len(salaries) for salaries in job_data]

# ---------------------------
# Plot
# ---------------------------
fig, ax = plt.subplots(figsize=(12, 5))

ax.boxplot(
    job_data,
    labels=job_titles,
    vert=False,
    showfliers=True,
    medianprops=dict(linewidth=2)
)

# Mean markers
ax.scatter(
    means,
    range(1, len(job_titles) + 1),
    marker='D',
    zorder=3,
    label='Mean Salary'
)

# Annotate medians & counts
for i, (median, count) in enumerate(zip(medians, counts), start=1):
    ax.text(
        median,
        i + 0.25,
        f"Median: ${median:,.0f}\n(n={count})",
        fontsize=9,
        ha='center'
    )

# Title & labels
ax.set_title(
    'Salary Distribution by Data Role in the United States',
    fontsize=14,
    weight='bold',
    pad=12
)
ax.set_xlabel('Annual Salary (USD)', fontsize=11)

# Currency formatting
ax.xaxis.set_major_formatter(
    FuncFormatter(lambda x, _: f'${x:,.0f}')
)

# Grid (x-axis only)
ax.grid(axis='x', linestyle='--', alpha=0.6)

# Legend
ax.legend(frameon=False)

plt.tight_layout()
plt.show()


