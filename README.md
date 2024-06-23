**Data Cleaning Documentation**

1) **Introduction**

The dataset messy\_data.csv provided for this project required extensive cleaning to prepare it for analysis. This document outlines the steps taken to clean the data and ensure its quality and consistency.

2) **Data Loading and Inspection**

The dataset was loaded into a Jupyter Notebook using Python and Pandas to analyze its structure and identify initial inconsistencies.

*<--python code-->*

*import pandas as pd*

- *Load the dataset*

*df = pd.read\_csv('messy\_data.csv')*

- *Inspect the data*

*print(df.head())*

*print(df.info())*

The dataset contains 11000  rows and 8 columns. Initial inspection revealed missing values in columns such as Age, Salary, Name, and Email.

3) **Handling Missing Values**

Approach:

- Missing Age values were filled with the median age.
- Salary values were filled with the mean salary.
- Rows with missing Name or Email were dropped from the dataset.

*<--python code-->*

*df['Age'] = df['Age'].fillna(df['Age'].median())*

*df['Salary'] = df['Salary'].fillna(df['Salary'].mean()) df.dropna(subset=['Name', 'Email'], inplace=True)*

4) **Removing Duplicates**

Approach:

Duplicate rows based on the ID column were identified and removed, keeping the first occurrence.

*<--python code-->*

*df = df.drop\_duplicates(subset='ID', keep='first')*

5) **Correcting Email Formats**

Approach:

Invalid email formats were identified using regular expressions and corrected to follow standard formats (username@domain.com). Only professional emails were retained.

*<--python code-->*

*import re*

*def is\_valid\_email(email):*

`    `*return re.match(r'^[a-zA-Z0-9.\_%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$', email) def is\_professional\_email(email):*

`    `*return not any(domain in email for domain in ['hotmail', 'yahoo', 'gmail']) df['Email'] = df['Email'].apply(lambda x: x if is\_valid\_email(x) else None) df.dropna(subset=['Email'], inplace=True)*

*df = df[df['Email'].apply(is\_professional\_email)]*

6) **Cleaning Name Fields**

Approach:

Names were cleaned to remove titles (Mr., Mrs., Ms., Dr., DDS, DVM) and any noise added to ensure consistent formatting.

*<--python code-->*

*def clean\_name(name):*

`    `*return re.sub(r'\b(Mr\.|Mrs\.|Ms\.|Dr\.|DDS|DVM)\b', '', name).strip() df['Name'] = df['Name'].apply(clean\_name)*

7) **Standardizing Date Formats**

Approach:

Dates in the Join Date column were standardized to YYYY-MM-DD format. Invalid dates default to 1970-01-01.

*<--python code-->*

*from datetime import datetime*

*def parse\_date(date\_str):*

`    `*for fmt in ('%Y-%m-%d', '%d/%m/%Y', '%m/%d/%Y'):*

`        `*try:*

`            `*return datetime.strptime(date\_str, fmt).strftime('%Y-%m-%d')*

`        `*except ValueError:*

`            `*pass*

`    `*return '1970-01-01'  # Default value if unable to parse*

*df['Join Date'] = df['Join Date'].apply(lambda x: parse\_date(x) if pd.notnull(x) else '1970-01-01')*

8) **Correcting Department Names**

Approach:

Typos and variations in department names (HR, Engineering, Marketing, Sales, Support) were identified and corrected to ensure consistency.

*<--python code-->*

*department\_mapping = {*

`    `*'hr': 'HR',*

`    `*'human resources': 'HR',*

`   `*'eng': 'Engineering',*

`    `*'engineering': 'Engineering',*

`    `*'mktg': 'Marketing',*

`    `*'marketing': 'Marketing',*

`    `*'sales': 'Sales',*

`    `*'support': 'Support',*

- *Add other variations as necessary*

*}*

*def clean\_and\_map\_department(dept):*

`    `*if isinstance(dept, str):*

`        `*dept = dept.lower()*

`        `*dept = re.sub(r'[^a-z\s]', '', dept)*

`        `*corrections = {*

`            `*'hr': r'h\s\*r|human\s\*resources|h\s\*r',*

`            `*'engineering': r'eng(?:ineering)?|engineeringi+',             'marketing': r'mktg|marketings?|marketingu+',             'sales': r'salesx?',*

`            `*'support': r'supporte?'*

`        `*}*

` `*for correct, pattern in corrections.items():*

`            `*if re.search(pattern, dept):*

`                `*return correct.capitalize()*

*return department\_mapping.get(dept, dept.capitalize())*

*else:*

`        `*return 'Unknown'*

*df['Department'] = df['Department'].apply(clean\_and\_map\_department)*

9) **Handling Salary Noise**

Approach:

Outliers in the Salary column were identified based on a reasonable range (30000 to 200000) and replaced with None. Missing salaries were filled with the mean salary.

*<--python code-->*

*min\_salary = 30000*

*max\_salary = 200000*

*df['Salary'] = pd.to\_numeric(df['Salary'], errors='coerce')*

*df.loc[(df['Salary'] < min\_salary) | (df['Salary'] > max\_salary), 'Salary'] = None df['Salary'] = df['Salary'].fillna(df['Salary'].mean())*

10) **Summary of Assumptions and Methodologies**

Assumptions were made regarding the nature of missing data and standard formats for emails, names, dates, and department names. Methodologies such as regular expressions, conditional logic, and pandas functions were used for data cleaning tasks.

11) **Conclusion**

The dataset “**messy\_data.csv**” has been successfully cleaned and prepared for further analysis. The process ensured data quality, consistency, and readiness for downstream tasks.

12) **Submission**

The cleaned dataset "**cleaned\_dataset.csv**" is included in the GitHub repository. A summary document (**README.md**) detailing the data cleaning process is provided 

alongside the dataset.
