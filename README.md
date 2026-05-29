# Overview

This project is a Python data analysis project focused on data-related job roles, particularly analyzing trends in job postings, in-demand skills, and salary trends across different data roles.

The project idea and dataset source were based on and inspired by a tutorial video, which I followed as a way to revise and strengthen my Python data analysis and visualization skills while building a portfolio project.

Project inspiration/tutorial:
https://youtu.be/wUSDVGivd-8?si=-igaKpvzQgcEqxU1

# Objectives of Project
1. What are the most demanded skills for Top 3 Data Roles?  
2. How are in-demand skills trending for Data Analyst?  
3. How well do jobs and skills pay for Data Analyst?  
4. What is the most optimal skill to learn for Data Analyst? (High Demand AND High Paying)

# Tools Used
- **Python**
    Python Libraries used: Pandas, Matplotlib, Seaborn
- **Jupyter Notebooks**
- **Visual Studio Code**
- **Git & GitHub**

# Data Preparation and Cleanup
## Filter MY and SG Jobs
To focus the analysis on the Malaysia job market, the dataset is filtered to include only roles based in Malaysia. However, due to the limited number of Data Analyst job postings in Malaysia for certain analyses and visualizations, Singapore data is also included.

# The Analysis
## Exploratory Data Analysis
Notebook for detailed steps: [1_EDA.ipynb](Data%20Jobs\1_EDA.ipynb)

Number of Data Roles in Malaysia
```python
df_plot_my = df_my['job_title_short'].value_counts().to_frame()

sns.set_theme(style = 'ticks')
sns.barplot(data=df_plot_my, x='count', y='job_title_short', hue='count', palette='dark:b_r', legend=False)

sns.despine()
plt.title('Malaysia: Number of Jobs per Title', fontweight='bold')
plt.xlabel('Number of Jobs')
plt.ylabel('')
```
![Visualization of Top Data Roles in Malaysia](Data%20Jobs\Image\Roles_Count_MY.png)

Companies with Most Data Roles in Malaysia
```python
df_plot_company_my = df_my['company_name'].value_counts().to_frame().head(20)

sns.set_theme(style='ticks')
sns.barplot(data=df_plot_company_my, x='count', y='company_name', hue='count', palette='dark:b_r', legend=False)

plt.title('Malaysia: Number of Jobs by Company', fontweight='bold')
plt.xlabel('Number of Jobs')
plt.ylabel('')
sns.despine()
```
![Visualization of Top Companies for Data Roles in Malaysia](Data%20Jobs\Image\Companies_Malaysia.png)

## 1. What are the most demanded skills for Top 3 Data Roles?
Notebook for detailed steps: [2_Skill_Demand.ipynb](Data%20Jobs\2_Skill_Demand.ipynb)

Top Skills for Data Roles in Malaysia
```python
fig,ax = plt.subplots(len(top3_job_titles), 1)

sns.set_theme(style='ticks')
for i, job_title in enumerate(top3_job_titles):
    df_plot = df_skills_perc[df_skills_perc['job_title_short'] == job_title].head(5)
    sns.barplot(data=df_plot, x='skill_percent', y='job_skills', ax=ax[i], hue='skill_count', palette='dark:b_r')
    ax[i].set_title(job_title)
    ax[i].set_ylabel('')
    ax[i].set_xlabel('')
    ax[i].get_legend().remove()
    ax[i].set_xlim(0, 65)
    # remove the x-axis tick labels for better readability
    if i != len(job_titles) - 1:
        ax[i].set_xticks([])

    # label the percentage on the bars
    for n, v in enumerate(df_plot['skill_percent']):
        ax[i].text(v + 1, n, f'{v:.0f}%', va='center')

fig.suptitle('Percentage of Skills Requested in Malaysia Job Postings', fontsize=13)
fig.tight_layout(h_pad=0.8)
```
![Visualization of Top Skills MY](Data%20Jobs\Image\TopRoles_Skills_Percent.png)

## 2. How are in-demand skills trending for Data Analyst?
Notebook for detailed steps: [3_Skill_Trend.ipynb](Data%20Jobs\3_Skill_Trend.ipynb)

In-demand Skills Trend by Month 
```python
df_plot = df_da_my_percent.iloc[:, :5]
sns.lineplot(data=df_plot, dashes=False, legend='full', palette='tab10')
sns.set_theme(style='ticks')
sns.despine()

plt.title('Trend of Top Skills for Data Analyst in Malaysia')
plt.ylabel('Percentage in each Data Analyst Role')
plt.xlabel('2023')
plt.legend().remove()
plt.gca().yaxis.set_major_formatter(PercentFormatter(decimals=0))

# annotate the plot with the top 5 skills using plt.text()
for i in range(5):
    plt.text(12.5, df_plot.iloc[-1, i], df_plot.columns[i], color='black')
```
![Visualization of Skills Trend](Data%20Jobs\Image\Skills_Trend_Month.png)

## 3. How well do jobs and skills pay for Data Analyst? 
Notebook for detailed steps: [4_Salary_Analysis.ipynb](Data%20Jobs\4_Salary_Analysis.ipynb)

Top Paid Skills and In-demand Skills Pay
```python
fig, ax = plt.subplots(2, 1)  

sns.set_theme(style='ticks')

# Top 10 Highest Paid Skills for Data Analysts
sns.barplot(data=df_da_my_top_pay, x='median', y=df_da_my_top_pay.index, hue='median', ax=ax[0], palette='dark:b_r')
ax[0].legend().remove()
ax[0].set_title('Highest Paid Skills for Data Analysts in Malaysia')
ax[0].set_ylabel('')
ax[0].set_xlabel('')
ax[0].xaxis.set_major_formatter(plt.FuncFormatter(lambda x, _: f'${int(x/1000)}K'))
ax[0].set_xlim(0, 120000)

# Top 10 Most Demanded Skills for Data Analysts')
sns.barplot(data=df_da_my_skills, x='median', y=df_da_my_skills.index, hue='median', ax=ax[1], palette='light:b')
ax[1].legend().remove()
ax[1].set_title('Most Demanded Skills for Data Analysts in Malaysia')
ax[1].set_ylabel('')
ax[1].set_xlabel('Median Salary (USD)')
ax[1].set_xlim(ax[0].get_xlim())  # Set the same x-axis limits as the first plot
ax[1].xaxis.set_major_formatter(plt.FuncFormatter(lambda x, _: f'${int(x/1000)}K'))

plt.tight_layout()
```
![Visualization of Skills and Pay](Data%20Jobs\Image\Skills_Pay.png)

## 4. What is the most optimal skill to learn for Data Analyst? (High Demand AND High Paying)
Notebook for detailed steps: [5_Optimal_Skills.ipynb](Data%20Jobs\5_Optimal_Skills.ipynb)

*Analysis is done for data in MY and SG

Skill by Salary and Data Analyst Roles Percentage

```python
sns.scatterplot(
    data=df_da_skills_tech_high_demand,
    x='skill_percent',
    y='median_salary',
    hue='technology'
)

sns.despine()
sns.set_theme(style='ticks')

# Prepare texts for adjustText
texts = []
for i, txt in enumerate(df_da_skills_high_demand.index):
    texts.append(plt.text(df_da_skills_high_demand['skill_percent'].iloc[i], df_da_skills_high_demand['median_salary'].iloc[i], txt))

# Adjust text to avoid overlap
adjust_text(texts, arrowprops=dict(arrowstyle='->', color='gray'))

# Set axis labels, title, and legend
plt.xlabel('Percent of Data Analyst Jobs')
plt.ylabel('Median Yearly Salary')
plt.title('Most Optimal Skills for Data Analysts in Malaysia & Singapore')
plt.legend(title='Technology')

from matplotlib.ticker import PercentFormatter
ax = plt.gca()
ax.yaxis.set_major_formatter(plt.FuncFormatter(lambda y, pos: f'${int(y/1000)}K'))
ax.xaxis.set_major_formatter(PercentFormatter(decimals=0))

plt.tight_layout()
```
![Visualization of Optimal Skills](Data%20Jobs\Image\Optimal_Skills.png)

# Insights
- **Skill Demand and Salary Correlation**: There is a clear correlation between the demand for specific skills and the salaries these skills command. Advanced and specialized skills like Python and Oracle often lead to higher salaries.
- **Market Trends**: There are changing trends in skill demand, highlighting the dynamic nature of the data job market. Keeping up with these trends is essential for career growth in data analytics.
- **Economic Value of Skills**: Understanding which skills are both in-demand and well-compensated can guide data analysts in prioritizing learning to maximize their economic returns.