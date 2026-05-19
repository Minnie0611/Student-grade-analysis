# Student Grade Analysis Project

## Project Overview

This project analyzes student academic performance using a student grade dataset.  
The goal of this project is to explore how different demographic, family, school, lifestyle, and study-related factors are associated with students' final grades.

This project focuses on data analysis rather than only prediction.  
The analysis includes data cleaning, exploratory data analysis, visualization, correlation analysis, hypothesis testing, effect size analysis, and confidence interval estimation.

## Authors

- Xirui Li
- Ruoyan Gong

## Project Files

```text
.
├── XiruiLi_RuoyanGong_StudentGrade.ipynb
├── grades_db(Assignment).csv
└── README.md
```

## Dataset Description

The dataset contains student-level information related to academic performance.

The dataset includes the following types of variables:

- Demographic information
- Family background information
- School-related information
- Study behavior
- Lifestyle factors
- Academic performance

Examples of variables include:

```text
sex
age
address
famsize
Pstatus
Medu
Fedu
Mjob
Fjob
studytime
failures
schoolsup
famsup
paid
activities
internet
romantic
absences
Grade
```

The main target variable is:

```text
Grade
```

`Grade` represents the student's final academic performance.

## Tools and Libraries

This project uses Python for data analysis.

Main libraries used in this project:

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from scipy import stats
import math
```

## Analysis Workflow

### 1. Data Loading

The dataset is loaded using Pandas.

```python
df = pd.read_csv('/content/sample_data/grades_db(Assignment).csv')
```

### 2. Data Understanding

The first step is to understand the dataset structure.

The notebook checks:

```python
df.head()
df.info()
df.describe()
df.shape
df.columns
```

This helps understand:

- Number of rows and columns
- Data types
- Missing values
- Basic summary statistics
- General dataset structure

## Data Cleaning

### 1. Missing Value Handling

Missing values are handled before analysis.

For categorical variables, missing values are filled with the mode.

```python
df['address'].fillna(df['address'].mode()[0], inplace=True)
```

For numerical variables, missing values are filled with the median.

```python
df['age'].fillna(df['age'].median(), inplace=True)
```

### 2. Outlier Detection

Boxplots are used to detect possible outliers in numerical variables.

```python
sns.boxplot(x=df['absences'])
plt.title('Boxplot of Absences')
plt.show()
```

Other numerical variables are also checked, such as:

```text
age
studytime
failures
absences
Grade
```

### 3. Outlier Treatment

The IQR method is used to identify outliers.

```python
Q1 = df['absences'].quantile(0.25)
Q3 = df['absences'].quantile(0.75)
IQR = Q3 - Q1

lower_bound = Q1 - 1.5 * IQR
upper_bound = Q3 + 1.5 * IQR
```

Outliers are replaced with the median value.

```python
median_value = df['absences'].median()

df['absences'] = np.where(
    (df['absences'] < lower_bound) | (df['absences'] > upper_bound),
    median_value,
    df['absences']
)
```

## Exploratory Data Analysis

### 1. Distribution Analysis

The distribution of numerical variables is visualized using histograms.

```python
df.hist(figsize=(12, 8))
plt.tight_layout()
plt.show()
```

This helps identify:

- Variable distribution
- Skewness
- Possible abnormal values
- General data patterns

### 2. Grade Distribution

The distribution of final grades is analyzed.

```python
sns.histplot(df['Grade'], kde=True)
plt.title('Distribution of Student Grades')
plt.xlabel('Grade')
plt.ylabel('Frequency')
plt.show()
```

This helps understand the overall academic performance of students.

### 3. Categorical Variable Analysis

Count plots are used to analyze categorical variables.

```python
sns.countplot(x='sex', data=df)
plt.title('Distribution of Gender')
plt.show()
```

Other categorical variables can also be visualized, such as:

```text
school
address
famsize
schoolsup
famsup
paid
activities
internet
romantic
higher
```

## Correlation Analysis

### 1. Correlation Matrix

A correlation matrix is used to analyze relationships among numerical variables.

```python
corr_matrix = df.corr(numeric_only=True)

plt.figure(figsize=(10, 8))
sns.heatmap(corr_matrix, annot=True, cmap='coolwarm')
plt.title('Correlation Heatmap')
plt.show()
```

### 2. Correlation with Grade

The correlation between numerical variables and `Grade` is examined.

```python
grade_corr = corr_matrix['Grade'].sort_values(ascending=False)
print(grade_corr)
```

This helps identify which numerical factors are more strongly associated with student grades.

Possible important variables include:

```text
studytime
failures
absences
age
parental education
```

## Bivariate Analysis

### 1. Study Time and Grade

The relationship between study time and final grade is analyzed.

```python
sns.boxplot(x='studytime', y='Grade', data=df)
plt.title('Study Time vs Grade')
plt.xlabel('Study Time')
plt.ylabel('Grade')
plt.show()
```

This helps determine whether students with longer study time tend to have higher grades.

### 2. Failures and Grade

Previous failures are compared with final grades.

```python
sns.boxplot(x='failures', y='Grade', data=df)
plt.title('Previous Failures vs Grade')
plt.xlabel('Number of Previous Failures')
plt.ylabel('Grade')
plt.show()
```

This helps evaluate whether past academic failure is related to current academic performance.

### 3. Absences and Grade

Absences are compared with final grades.

```python
sns.scatterplot(x='absences', y='Grade', data=df)
plt.title('Absences vs Grade')
plt.xlabel('Absences')
plt.ylabel('Grade')
plt.show()
```

This helps analyze whether student attendance is related to academic outcomes.

## Statistical Analysis

### 1. Kruskal-Wallis Test

The Kruskal-Wallis test is used to test whether grades differ significantly across study time groups.

```python
groups = [group['Grade'].values for name, group in df.groupby('studytime')]

stat, p_value = stats.kruskal(*groups)

print("Kruskal-Wallis Statistic:", stat)
print("p-value:", p_value)
```

Interpretation:

```text
If p-value < 0.05:
    There is a statistically significant difference in grades among different study time groups.
Else:
    There is no statistically significant difference in grades among different study time groups.
```

### 2. ANOVA Test

ANOVA is used as a sensitivity analysis.

```python
groups = [group['Grade'].values for name, group in df.groupby('studytime')]

f_stat, p_value = stats.f_oneway(*groups)

print("ANOVA F-statistic:", f_stat)
print("p-value:", p_value)
```

This checks whether the result is consistent with the Kruskal-Wallis test.

### 3. Independent T-Test

The independent t-test is used to compare the average grades between two groups.

Example: comparing grades between male and female students.

```python
male_grades = df[df['sex'] == 'M']['Grade']
female_grades = df[df['sex'] == 'F']['Grade']

t_stat, p_value = stats.ttest_ind(male_grades, female_grades, equal_var=False)

print("T-statistic:", t_stat)
print("p-value:", p_value)
```

Interpretation:

```text
If p-value < 0.05:
    The average grades between the two groups are significantly different.
Else:
    There is no statistically significant difference between the two groups.
```

## Pass / Fail Analysis

The final grade is converted into a binary pass/fail variable.

```python
df['pass_fail'] = df['Grade'].apply(lambda x: 'Pass' if x >= 10 else 'Fail')
```

Grade rule:

```text
Grade >= 10: Pass
Grade < 10: Fail
```

### Chi-Square Test

The Chi-Square test is used to examine relationships between categorical variables and pass/fail status.

```python
contingency_table = pd.crosstab(df['schoolsup'], df['pass_fail'])

chi2, p_value, dof, expected = stats.chi2_contingency(contingency_table)

print("Chi-square statistic:", chi2)
print("p-value:", p_value)
print("Degrees of freedom:", dof)
```

Interpretation:

```text
If p-value < 0.05:
    The categorical variable is significantly associated with pass/fail status.
Else:
    The categorical variable is not significantly associated with pass/fail status.
```

## Effect Size Analysis

Cohen's d is used to measure the practical difference between two groups.

```python
def cohens_d(group1, group2):
    mean1 = np.mean(group1)
    mean2 = np.mean(group2)
    std1 = np.std(group1, ddof=1)
    std2 = np.std(group2, ddof=1)
    
    pooled_std = math.sqrt(((len(group1) - 1) * std1 ** 2 + (len(group2) - 1) * std2 ** 2) / 
                           (len(group1) + len(group2) - 2))
    
    return (mean1 - mean2) / pooled_std
```

Example:

```python
effect_size = cohens_d(male_grades, female_grades)
print("Cohen's d:", effect_size)
```

Interpretation:

```text
0.2 = small effect
0.5 = medium effect
0.8 = large effect
```

Effect size is important because statistical significance alone does not show whether the difference is practically meaningful.

## Confidence Interval Analysis

A 95% confidence interval is calculated for the mean grade.

```python
mean_grade = df['Grade'].mean()
std_grade = df['Grade'].std()
n = len(df)

confidence_level = 0.95
alpha = 1 - confidence_level

t_critical = stats.t.ppf(1 - alpha / 2, df=n-1)
margin_error = t_critical * (std_grade / np.sqrt(n))

lower_bound = mean_grade - margin_error
upper_bound = mean_grade + margin_error

print("Mean Grade:", mean_grade)
print("95% Confidence Interval:", lower_bound, upper_bound)
```

Interpretation:

```text
The 95% confidence interval provides a range in which the true population mean grade is likely to fall.
```

## Key Findings

Based on the analysis, the project focuses on several important factors related to student grades:

```text
studytime
failures
absences
school support
family support
parental education
higher education intention
alcohol consumption
romantic relationship status
```

Main findings include:

- Study behavior may be related to student academic performance.
- Previous failures are likely associated with lower grades.
- Absences may negatively affect academic outcomes.
- School and family support may have different relationships with student performance.
- Some categorical variables may be associated with pass/fail status.
- Statistical tests help determine whether observed differences are significant.
- Effect size analysis helps evaluate whether differences are practically meaningful.

## How to Run the Project

### Step 1: Clone or Download the Project

```bash
git clone <your-repository-link>
cd <your-project-folder>
```

### Step 2: Install Required Libraries

```bash
pip install pandas numpy matplotlib seaborn scipy
```

### Step 3: Open the Notebook

You can open the notebook using Jupyter Notebook:

```bash
jupyter notebook XiruiLi_RuoyanGong_StudentGrade.ipynb
```

Or open it in Google Colab.

### Step 4: Check Dataset Path

Make sure the dataset path is correct.

Current notebook path:

```python
df = pd.read_csv('/content/sample_data/grades_db(Assignment).csv')
```

If running locally, change it to:

```python
df = pd.read_csv('grades_db(Assignment).csv')
```

### Step 5: Run All Cells

Run the notebook from top to bottom.

## Project Purpose

This project demonstrates a complete data analysis workflow, including:

```text
Data loading
Data understanding
Data cleaning
Missing value handling
Outlier detection
Outlier treatment
Exploratory data analysis
Data visualization
Correlation analysis
Hypothesis testing
Effect size analysis
Confidence interval estimation
Result interpretation
```

## Future Improvements

Possible future improvements include:

```text
Build predictive models to predict final grades
Create a classification model to predict pass/fail status
Use feature engineering to improve model performance
Compare Logistic Regression, Decision Tree, Random Forest, and XGBoost
Create a Streamlit dashboard for interactive analysis
Add more visualizations for categorical variables
Use cross-validation for model evaluation
```

## Conclusion

This project provides a data analysis workflow for understanding student academic performance.

Through data cleaning, visualization, correlation analysis, hypothesis testing, effect size analysis, and confidence interval estimation, the project identifies several factors that may be related to students' final grades.

The analysis shows that student performance is not influenced by only one factor. Instead, academic outcomes may be related to a combination of study behavior, previous academic history, family background, school support, and lifestyle factors.

This project can help educators, researchers, and students better understand the possible drivers of academic performance and provide insights for improving student support strategies.
