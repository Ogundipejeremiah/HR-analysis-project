# HR-analysis-project

## Table of contents
- [Project overview](#Project-overview)
- [Data sources](#Data-sources)
- [Tools](#Tool)
- [Data Cleaning/Preparation](#Data-Cleaning/Preparation)
- [Exploratory Data Analysis (EDA)](#Exploratory-Data-Analysis-(EDA))
- [Data analysis](#Data-analysis)
- [Results/Findings](#Results/Findings)
- [Recommendations](#Recommendations)

### Project overview


This project focuses on leveraging SQL, Python, and Tableau to analyze HR data, uncover actionable insights, and monitor key workforce metrics. The project involves building a data pipeline to acquire, clean, and process HR datasets, followed by exploratory data analysis (EDA) to identify trends. Finally, interactive KPI dashboards are created in Tableau to visualize and track essential workforce metrics, enabling data-driven decision-making in HR management.

### Data sources

HR Data: The primary dataset used for this analysis is the 'HR Data.csv' file, which contains detailed information about the company's workforce.

### Tool

- SQL Server - Data Analysis
- Tableau - Creating reports
- Python(Jupyter notebook) - Data cleaning and analysis

### Data Cleaning/Preparation

In the initial data preparation phase, we performed the following tasks:
1. Data loading and inspection.
2. Handling missing values.
3. Data Cleaning and formatting.

### Exploratory Data Analysis (EDA)
EDA involved exploring the company's workforce to answer various questions, such as;

1. What factors drive attrition across departments, and how does gender affect these trends?
2. How does age impact attrition, and which age groups are most at risk?
3. Is there a link between job satisfaction and attrition, and how does this vary by role?

### Data analysis

Includes some codes/features worked with

```sql
SQL for KPIs
1. Employee Count (Total Employees)
This will return the total number of employees in the company:
SELECT 
    SUM(employee_count) AS total_employee_count FROM hr_data;
2. Attrition Count (Employees Who Left)
This will sum up the employees who left:
SELECT 
    COUNT(*) AS attrition_count FROM hr_data WHERE attrition = 'Yes';
3. Attrition Rate
This query calculates the percentage of employees who left:
SELECT 
    (COUNT(CASE WHEN attrition = 'Yes' THEN 1 END) / MAX(employee_count)) * 100 AS attrition_rate FROM hr_data;
4. Active Employees
This will calculate the count of active employees:
SELECT 
    COUNT(*) AS active_employees FROM hr_data WHERE attrition = 'No';
5. Average Age
To calculate the average age:
SELECT 
    AVG(age) AS average_age FROM hr_data;

SQL for Dashboard Visualizations
1. Attrition by Gender
SELECT 
    Gender, 
    COUNT(*) AS attrition_count FROM hr_data WHERE attrition = 'Yes' GROUP BY Gender;
2. Department-wise Attrition
SELECT 
    Department, 
    COUNT(*) AS attrition_count FROM hr_data WHERE attrition = 'Yes' GROUP BY Department;
3. Number of Employees by Age Group
SELECT 
    CASE 
        WHEN age < 25 THEN 'Under 25'
        WHEN age BETWEEN 25 AND 34 THEN '25-34'
        WHEN age BETWEEN 35 AND 44 THEN '35-44'
        WHEN age BETWEEN 45 AND 54 THEN '45-54'
        ELSE '55+'
    END AS age_group,
    COUNT(*) AS employee_count FROM hr_data GROUP BY 
    CASE 
        WHEN age < 25 THEN 'Under 25'
        WHEN age BETWEEN 25 AND 34 THEN '25-34'
        WHEN age BETWEEN 35 AND 44 THEN '35-44'
        WHEN age BETWEEN 45 AND 54 THEN '45-54'
        ELSE '55+'
    END;
4. Job Satisfaction Rating (Job Role vs. Job Satisfaction)
SELECT 
    job_role, 
    AVG(job_satisfaction) AS avg_job_satisfaction FROM hr_data GROUP BY job_role;
5. Education Field-wise Attrition
SELECT 
    Education, 
    COUNT(*) AS attrition_count FROM hr_data WHERE attrition = 'Yes' GROUP BY Education;
6. Attrition Rate by Gender for Different Age Groups
SELECT 
    gender, 
    CASE 
        WHEN age < 25 THEN 'Under 25'
        WHEN age BETWEEN 25 AND 34 THEN '25-34'
        WHEN age BETWEEN 35 AND 44 THEN '35-44'
        WHEN age BETWEEN 45 AND 54 THEN '45-54'
        ELSE '55+'
    END AS age_group,
    (COUNT(CASE WHEN attrition = 'Yes' THEN 1 END) * 1.0 / MAX(employee_count)) * 100 AS attrition_rate FROM hr_data GROUP BY gender, 
    CASE 
        WHEN age < 25 THEN 'Under 25'
        WHEN age BETWEEN 25 AND 34 THEN '25-34'
        WHEN age BETWEEN 35 AND 44 THEN '35-44'
        WHEN age BETWEEN 45 AND 54 THEN '45-54'
        ELSE '55+'
    END;
```

### Python code

```import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
import numpy as np
hr_data=pd.read_csv('HR Data.csv')
hr_data

# checking for missing values
hr_data.isnull().sum()
# checking for duplicated values
hr_data.duplicated().sum()
# Data analysis

# 1. **Employee Count**
employee_count = hr_data['Employee Count'].count()
# 2. **Attrition Count**
attrition_count = hr_data[hr_data['Attrition'] == 'Yes'].shape[0]
# 3. **Attrition Rate**
attrition_rate = (attrition_count / employee_count) * 100
# 4. **Active Employees**
active_employees = hr_data[hr_data['Attrition'] == 'No'].shape[0]
# 5. **Average Age**
average_age = hr_data['Age'].mean()
1.# KPIs
print("Employee Count:", employee_count)
print("Attrition Count:", attrition_count)
print("Attrition Rate:", round(attrition_rate, 2), "%")
print("Active Employees:", active_employees)
print("Average Age:", round(average_age, 0))

2.
# Calculate Attrition Rate by Gender for Different Age Groups
# Group by gender and age group, count attrition (Yes) and total employees
attrition_rate_by_gender_age_group = hr_data.groupby(['Gender', 'CF_age band'])['Attrition'].value_counts(normalize=True).unstack().fillna(0)

# Calculate the attrition rate (percentage of Yes for each gender-age group combination)
attrition_rate_by_gender_age_group['Attrition_rate'] = attrition_rate_by_gender_age_group['Yes'] * 100
# Prepare to plot
fig, axes = plt.subplots(5, 1, figsize=(10, 18))  # 5 rows, 1 column for each age group
# List of age group labels for easier iteration
age_groups = ["Under 25", "25-34", "35-44", "45-54", "55+"]

# Loop through the age groups and plot male/female attrition rates in the same chart
for i, age_group in enumerate(age_groups):
    # Extract the attrition rates for male and female
    male_attrition_rate = attrition_rate_by_gender_age_group.loc[("Male", age_group), "Attrition_rate"]
    female_attrition_rate = attrition_rate_by_gender_age_group.loc[("Female", age_group), "Attrition_rate"]
    
    # Combine the attrition rate for male and female for plotting
    data = [male_attrition_rate, female_attrition_rate]
    
    # Plot the combined donut chart for Male and Female in the same subplot
    wedges, texts, autotexts = axes[i].pie(data, labels=["Male Attrition", "Female Attrition"],
                                          autopct='%1.1f%%', startangle=90, wedgeprops={'width': 0.3, 'edgecolor': 'w'},
                                          colors=['#ff9999', '#66b3ff'])
    
    # Set title for each subplot
    axes[i].set_title(f"Attrition Rate by Gender - {age_group}")

# Add a single shared legend for all subplots at the top
fig.legend(wedges, ['Male Attrition', 'Female Attrition'], title="Legend", loc="upper center", bbox_to_anchor=(0.5, 1.1), ncol=2)

# Add a main title for the entire figure
fig.suptitle("Attrition Rate by Gender for Different Age Groups", fontsize=16)

# Adjust layout to make space for the titles and legend
plt.tight_layout(rect=[0, 0.03, 1, 0.95])

# Display the plot
plt.show()
# 3. **Attrition by Gender**
plt.figure(figsize=(8, 6))
sns.countplot(data=hr_data, x='Gender', hue='Attrition', palette='coolwarm')
plt.title('Attrition by Gender')
plt.xlabel('Gender')
plt.ylabel('Count')
plt.show()
# 4. **Job Satisfaction Rating (Job Role vs Job Satisfaction)** (Boxplot)
plt.figure(figsize=(10, 6))
sns.boxplot(x="Job Role", y="Job Satisfaction", data=hr_data, palette="Set1")
plt.title("Job Satisfaction by Job Role", fontsize=14)
plt.xlabel("Job Role")
plt.ylabel("Job Satisfaction")
plt.xticks(rotation=45)
plt.show()
# 5. **Department-wise Attrition (Pie Chart)**
plt.figure(figsize=(8, 6))
department_attrition = hr_data[hr_data["Attrition"] == "Yes"]['Department'].value_counts()
department_attrition.plot(kind='pie', autopct='%1.1f%%', startangle=90, colors=['#ff9999','#66b3ff','#99ff99'])
plt.title('Department-wise Attrition')
plt.ylabel('')  # Remove y-label for clarity
plt.show()

# 6. **Education Field-wise Attrition (Bar Chart)**
plt.figure(figsize=(8, 6))
education_field_attrition = hr_data[hr_data["Attrition"] == "Yes"]['Education Field'].value_counts()
education_field_attrition.plot(kind='bar', color=['#ff9999','#66b3ff','#99ff99'], width=0.8)
plt.title('Attrition by Education Field')
plt.xlabel('Education Field')
plt.ylabel('Count')
plt.xticks(rotation=45)
plt.show()

# 7. **Employees by Age Group (Bar Chart)**
hr_data["CF_age band"] = pd.cut(
    hr_data["Age"],
    bins=[0, 24, 34, 44, 54, float("inf")],
    labels=["Under 25", "25-34", "35-44", "45-54", "55+"],
)
plt.figure(figsize=(8, 6))
employees_by_age_group = hr_data['CF_age band'].value_counts()
employees_by_age_group.plot(kind='bar', color=['#ff9999','#66b3ff','#99ff99','#c2c2f0','#ffcc99'])
plt.title('Number of Employees by Age Group')
plt.xlabel('Age Group')
plt.ylabel('Count')
plt.xticks(rotation=45)
plt.show()

```
### Results/Findings

The analysis results are summarized as follows;
1. (a) Higher attrition rates in Sales and Customer Service.
   (b) Female employees have higher attrition in Sales, while male employees have higher attrition in IT and Engineering.
2. (a) Highest attrition rates in employees under 25 and between 45-54.
   (b) Younger employees leave due to limited growth opportunities, while older employees leave due to retirement or job dissatisfaction.
3. (a) Employees in Sales and Customer Support show higher attrition due to low job satisfaction.
   (b) Stronger correlation in customer-facing roles, with dissatisfaction linked to work-life balance and compensation.

### Recommendations
Based on the analysis, the following are recommended;
1. For high attrition departments: Focus on understanding specific challenges and implement targeted retention programs such as career development opportunities and stress reduction initiatives.
2. For gender-based trends: Offer flexible work arrangements and mentorship opportunities to retain female employees in male-dominated departments like IT.
3. For younger employees: Provide growth and training opportunities, mentorship programs, and clear career paths.
4. For older employees: Offer retirement planning assistance or transitioning programs for longer engagement in less demanding roles.
5. For low job satisfaction roles: Conduct targeted surveys to identify dissatisfaction drivers and improve work-life balance, compensation packages, and employee engagement.
6. For high-risk groups: Offer retention bonuses, job flexibility, and clear career progression paths in roles with high attrition linked to low job satisfaction.





