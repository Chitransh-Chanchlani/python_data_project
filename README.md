# Introduction

Welcome to my analysis of the data job market, focusing on data analyst roles. This project was created out of a desire to navigate and understand the job market more effectively. It delves into the top-paying and in-demand skills to help find optimal job opportunities for data analysts.

The data sourced from [Luke Barousse's Python](https://www.lukebarousse.com/python) Course which provides a foundation for my analysis, containing detailed information on job titles, salaries, locations, and essential skills. Through a series of Python scripts, I explore key questions such as the most demanded skills, salary trends, and the intersection of demand and salary in data analytics.

# Important Queskions

Below are the questions I want to answer in my project:

1. What are the skills most in demand for the top 3 most popular data roles?
2. How are in-demand skills trending for Data Analysts?
3. How well do jobs and skills pay for Data Analysts?
4. What are the optimal skills for data analysts to learn? (High Demand AND High Paying)

# Tools Used

For my deep dive into the data analyst job market, I harnessed the power of several key tools:
- Python: The backbone of my analysis, allowing me to analyze the data and find critical insights.I also used the following Python libraries:
    - Pandas Library: This was used to analyze the data.
    - Matplotlib Library: I visualized the data.
    - Seaborn Library: Helped me create more advanced visuals.
    - Jupyter Notebooks: The tool I used to run my Python scripts which let me easily include my notes and analysis.
- Visual Studio Code: My go-to for executing my Python scripts.
- Git & GitHub: Essential for version control and sharing my Python code and analysis, ensuring collaboration and project tracking.

# Data Preparation and Cleanup

This section outlines the steps taken to prepare the data for analysis, ensuring accuracy and usability.

## Import & Clean Up Data

I start by importing necessary libraries and loading the dataset, followed by initial data cleaning tasks to ensure data quality.


```python
# Importing Libraries
import ast
import pandas as pd
import seaborn as sns
from datasets import load_dataset
import matplotlib.pyplot as plt

# Loading Data
dataset = load_dataset('lukebarousse/data_jobs')
df = dataset['train'].to_pandas()

# Data Cleanup
df['job_posted_date'] = pd.to_datetime(df['job_posted_date'])
df['job_skills'] = df['job_skills'].apply(lambda x: ast.literal_eval(x) if pd.notna(x) else x)
```
## Filter US Jobs

To focus my analysis on the U.S. job market, I apply filters to the dataset, narrowing down to roles based in the United States.

```python 
df_US = df[df['job_country'] == 'United States']
```

# The Analysis

Each Jupyter notebook for this project aimed at investigating specific aspects of the data job market. Here’s how I approached each question:

## 1. What are the most demanded skills for the top 3 most popular data roles?

To find the most demanded skills for the top 3 most popular data roles. I filtered out those positions by which ones were the most popular, and got the top 5 skills for these top 3 roles. This query highlights the most popular job titles and their top skills, showing which skills I should pay attention to depending on the role I'm targeting.

View my notebook with detailed steps here: [2_Skill_Demand](3_Project/2_skills_counting.ipynb).

### Visualize Data

```python
fig, ax = plt.subplots(len(job_titles),1)

sns.set_theme(style = 'ticks')
rc= {'patch.edgecolor': 'none','patch.linewidth': 0}

for i, job_title in enumerate(job_titles):
   df_plot = df_skills_perc[df_skills_perc['job_title_short'] == job_title].head(5)
   sns.barplot(data = df_plot, x = 'skill_perc', y = 'job_skills', ax = ax[i], hue = 'skill_count', palette = 'dark:b_r' )
   ax[i].spines[['top', 'right']].set_visible(False)    # removing top and side borders
   ax[i].set_xlabel('')
   ax[i].set_ylabel('')
   ax[i].legend().set_visible(False)
   ax[i].set_xlim(0,78)


# showing precent values in front of the bars, creating proper space and using proper format
   for n, v in enumerate(df_plot['skill_perc']):
        ax[i].text(v + 1, n, f"{v:.0f}%", va='center')


fig.suptitle('Counts of Top Skills in Job Postings', fontsize = 15)
fig.tight_layout()
plt.show()
```

### Results

![Visualization of Top Skill for Data Nerds](3_Project/images/Skill_demand_all_data_role.png)

### Insights:
- SQL is the most requested skill for Data Analysts and Data Scientists, with it in over half the job postings for both roles. For Data Engineers, Python is the most sought-after skill, appearing in 68% of job postings.
- Data Engineers require more specialized technical skills (AWS, Azure, Spark) compared to Data Analysts and Data Scientists who are expected to be proficient in more general data management and analysis tools (Excel, Tableau).
- Python is a versatile skill, highly demanded across all three roles, but most prominently for Data Scientists (72%) and Data Engineers (65%).

## 2. How are in-demand skills trending for Data Analysts?

To find how skills are trending in 2023 for Data Analysts, I filtered data analyst positions and grouped the skills by the month of the job postings. This got me the top 5 skills of data analysts by month, showing how popular skills were throughout 2023.

View my notebook with detailed steps here: [3_Skills_Trend](3_Project/3_Skill_Trend.ipynb).

### Visualize Data

```python
# plotting the top 5 skills in demand
df_plot = df_da_perc.iloc[:,:5]
sns.lineplot(data = df_plot, dashes = False, palette = 'tab10')
sns.despine()

plt.title('Trending Top Skills for Data Analyst in US')
plt.ylabel('Likelihood in Job Posting')
plt.xlabel(2023)
plt.legend().remove()

# formatting the text on the y axis and on the lines with percentformatter from matplotlib
from matplotlib.ticker import PercentFormatter
ax = plt.gca()
ax.yaxis.set_major_formatter(PercentFormatter(decimals = 0))
for i in range(5): 
		plt.text(11.2, df_plot.iloc[-1,i], df_plot.columns[i]) # using this to apply text on the lines
```

### Insights:

- SQL remains the most consistently demanded skill throughout the year, although it shows a gradual decrease in demand.
- Excel experienced a significant increase in demand starting around September, surpassing both Python and Tableau by the end of the year.
- Both Python and Tableau show relatively stable demand throughout the year with some fluctuations but remain essential skills for data analysts. Power BI, while less demanded compared to the others, shows a slight upward trend towards the year's end.

## 3. How well do jobs and skills pay for Data Analysts?

To identify the highest-paying roles and skills, I only got jobs in the United States and looked at their median salary. But first I looked at the salary distributions of common data jobs like Data Scientist, Data Engineer, and Data Analyst, to get an idea of which jobs are paid the most.

View my notebook with detailed steps here: [4_Salary_Analysis](3_Project/4_Salary_Analysis.ipynb).

### Visualize Data

```python
# using sns boxplot to plot the data

sns.boxplot(data = df_us_top6, x = 'salary_year_avg', y = 'job_title_short', order = job_order)
sns.set_theme(style = 'ticks')
plt.title('Salary Distribution in the US')
plt.xlabel('Yearly salary in USD')
plt.ylabel('')
plt.xlim(0,60000)

# setting x-axis in desired format
ax = plt.gca()
ax.xaxis.set_major_formatter(plt.FuncFormatter(lambda x, pos: f'${int(x/1000)}K'))
plt.xlim(0,600000)
plt.show()
```

### Results

![Visualization of Top Salary Distributions](3_Project/images/Salary_distributions_data_role.png)

### Insights

- There's a significant variation in salary ranges across different job titles. Senior Data Scientist positions tend to have the highest salary potential, with up to $600K, indicating the high value placed on advanced data skills and experience in the industry.

- Senior Data Engineer and Senior Data Scientist roles show a considerable number of outliers on the higher end of the salary spectrum, suggesting that exceptional skills or circumstances can lead to high pay in these roles. In contrast, Data Analyst roles demonstrate more consistency in salary, with fewer outliers.

- The median salaries increase with the seniority and specialization of the roles. Senior roles (Senior Data Scientist, Senior Data Engineer) not only have higher median salaries but also larger differences in typical salaries, reflecting greater variance in compensation as responsibilities increase.

### Highest Paid & Most Demanded Skills for Data Analysts

Next, I narrowed my analysis and focused only on data analyst roles. I looked at the highest-paid skills and the most in-demand skills. I used two bar charts to showcase these.

### Visualize data

```python
fig, ax = plt.subplots(2,1)

sns.set_theme(style='ticks')
# Top 10 Highest Paid Skills for Data Analysis
# using seaborn to plot our data, to put the colors on the bar first we need to define the hue which in this case is median then decide the color palette
# and with adding _r in the palette we can reverse the color oalette of the bars
sns.barplot(data = df_da_top_salary, x = 'median', y = df_da_top_salary.index, ax = ax[0], hue = 'median', palette = 'dark:b_r')

# df_da_top_salary.plot(kind = 'barh', y = 'median', ax = ax[0], legend = False)
# ax[0].invert_yaxis()
ax[0].legend().remove()
ax[0].set_title('Top 10 Highest Paid Skills for Data Analysts in US')
ax[0].set_xlabel('')
ax[0].set_ylabel('')
ax[1].set_xlim(ax[0].get_xlim())  # we can set the x limit of the 1 graph to the 2nd graph
ax[0].xaxis.set_major_formatter(plt.FuncFormatter(lambda x, pos: f'${int(x/1000)}K'))

# Top 10 in-demand Skills for Data Analysis
# using seaborn to plot our data, to put the colors on the bar first we need to define the hue which in this case is median then decide the color palette

sns.barplot(data = df_da_skills , x = 'median', y = df_da_skills.index, ax = ax[1], hue = 'median', palette = 'light:b')

# df_da_skills.plot(kind = 'barh', y = 'median', ax = ax[1], legend = False)
# ax[1].invert_yaxis()
ax[1].legend().remove()
ax[1].set_title('Top 10 most in-demand skills for Data Analysts in US')
ax[1].set_xlabel('')
ax[1].set_ylabel('Median Salary (USD)')
ax[1].xaxis.set_major_formatter(plt.FuncFormatter(lambda x, pos: f'${int(x/1000)}K'))

plt.tight_layout()
plt.show()
```

### Results

Here's the breakdown of the highest-paid & most in-demand skills for data analysts in the US:

![Visualization of Highest paid skills for Data Analyst in US]()