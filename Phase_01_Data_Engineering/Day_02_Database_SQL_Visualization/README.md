\# Day 2 — Student Reference Guide

\## Database Fundamentals + SQL + Data Visualization

\### Codeboosters Tech — Data Engineering + GenAI Internship



\---



\## Continuity from Day 1

The `student\_performance.csv` from Day 1 is \*\*loaded into a real SQLite database\*\* today.

Pipeline: `CSV → SQLite DB → SQL Queries → DataFrames → Charts`



\---



\## Database Quick Concepts



| Term | Meaning | Example |

|------|---------|---------|

| \*\*Database\*\* | Organized storage for structured data | `college.db` |

| \*\*Table\*\* | Rows and columns inside a database | `students` table |

| \*\*Row / Record\*\* | One complete entry | One student's data |

| \*\*Column / Field\*\* | One attribute per record | `math\_score` |

| \*\*Primary Key\*\* | Unique ID for each row — cannot repeat | `student\_id` |

| \*\*Foreign Key\*\* | Column linking to another table's PK | `dept\_code` |

| \*\*SQL\*\* | Language to query databases | `SELECT \* FROM students` |

| \*\*SQLite\*\* | Lightweight DB stored in one file | `college.db` |



\---



\## Python Database Setup — Essential Code



```python

import sqlite3

import pandas as pd

import matplotlib.pyplot as plt



\# 1. Connect to (or create) database

conn = sqlite3.connect('college.db')

cursor = conn.cursor()



\# 2. Load CSV into database table

df = pd.read\_csv('student\_performance.csv')

df.to\_sql('students', conn, if\_exists='replace', index=False)



\# 3. Run SQL → get DataFrame

result = pd.read\_sql\_query("SELECT \* FROM students LIMIT 5", conn)



\# 4. Close when done

conn.close()

```



\---



\## SQL Cheat Sheet



```sql

\-- Basic SELECT

SELECT name, math\_score FROM students;

SELECT \* FROM students;                        -- all columns

SELECT \* FROM students LIMIT 10;              -- first 10 rows



\-- Filter with WHERE

SELECT name FROM students WHERE math\_score > 80;

SELECT name FROM students WHERE department = 'Computer Science';

SELECT name FROM students WHERE math\_score > 70 AND attendance\_percentage > 85;

SELECT name FROM students WHERE department IN ('Computer Science', 'Electronics');

SELECT name FROM students WHERE math\_score BETWEEN 70 AND 90;



\-- Sort and limit

SELECT name, math\_score FROM students ORDER BY math\_score DESC LIMIT 5;

SELECT DISTINCT department FROM students;      -- unique values



\-- Aggregate functions

SELECT COUNT(\*)                 FROM students;             -- total rows

SELECT AVG(math\_score)          FROM students;             -- average

SELECT SUM(attendance\_percentage) FROM students;           -- sum

SELECT MIN(math\_score)          FROM students;             -- minimum

SELECT MAX(math\_score)          FROM students;             -- maximum

SELECT ROUND(AVG(math\_score),2) FROM students;             -- rounded avg



\-- GROUP BY

SELECT department, COUNT(\*) AS num\_students

FROM students GROUP BY department;



SELECT department, ROUND(AVG(math\_score),2) AS avg\_math

FROM students GROUP BY department ORDER BY avg\_math DESC;



\-- HAVING (filter groups)

SELECT department, AVG(math\_score) AS avg\_math

FROM students GROUP BY department HAVING AVG(math\_score) > 70;



\-- Calculated column

SELECT name, math\_score + science\_score + english\_score + programming\_score AS total\_score

FROM students ORDER BY total\_score DESC;



\-- INNER JOIN

SELECT s.name, s.math\_score, d.hod\_name

FROM students AS s

INNER JOIN departments AS d ON s.dept\_code = d.dept\_code;

```



\*\*SQL Execution Order:\*\* `FROM → WHERE → GROUP BY → HAVING → SELECT → ORDER BY → LIMIT`



\---



\## WHERE vs HAVING



| | WHERE | HAVING |

|--|-------|--------|

| \*\*Filters\*\* | Individual rows | Groups |

| \*\*When applied\*\* | Before GROUP BY | After GROUP BY |

| \*\*Can use aggregates?\*\* | No | Yes |

| \*\*Example\*\* | `WHERE math\_score > 80` | `HAVING AVG(math\_score) > 80` |



\---



\## Matplotlib Chart Templates



```python

import matplotlib.pyplot as plt



\# ── Template (works for all chart types) ──

fig, ax = plt.subplots(figsize=(10, 6))   # width x height in inches

\# ... draw chart here ...

ax.set\_title('Title Here', fontsize=16, fontweight='bold')

ax.set\_xlabel('X Label', fontsize=13)

ax.set\_ylabel('Y Label', fontsize=13)

plt.tight\_layout()

plt.show()



\# ── Bar Chart ──

ax.bar(x\_categories, y\_values, color='#4FC3F7', edgecolor='white')



\# ── Histogram ──

ax.hist(data\_list, bins=10, color='#4FC3F7', edgecolor='white')

ax.axvline(x=mean\_val, color='red', linestyle='--', label='Mean')

ax.legend()



\# ── Horizontal Bar ──

ax.barh(y\_labels, x\_values, color='#4FC3F7')

ax.invert\_yaxis()                          # highest value at top



\# ── Pie Chart ──

ax.pie(sizes, labels=labels, autopct='%1.1f%%', startangle=90,

&#x20;      wedgeprops={'edgecolor':'white', 'linewidth':2})



\# ── 4-Panel Dashboard ──

fig, axes = plt.subplots(2, 2, figsize=(18, 13))

fig.suptitle('Dashboard Title', fontsize=20, fontweight='bold')

ax1 = axes\[0]\[0]   # top-left

ax2 = axes\[0]\[1]   # top-right

ax3 = axes\[1]\[0]   # bottom-left

ax4 = axes\[1]\[1]   # bottom-right

```



\---



\## pd.read\_sql\_query — The Bridge



```python

\# SQL → DataFrame in one line

result\_df = pd.read\_sql\_query("""

&#x20;   SELECT department, ROUND(AVG(math\_score), 2) AS avg\_math

&#x20;   FROM students

&#x20;   GROUP BY department

&#x20;   ORDER BY avg\_math DESC

""", conn)



\# Now use result\_df like any other DataFrame

print(result\_df)

ax.bar(result\_df\['department'], result\_df\['avg\_math'])

```



\---



\## Common Errors — Day 2



| Error | Cause | Fix |

|-------|--------|-----|

| `no such table: students` | DB setup cell not run | Run the `df.to\_sql()` cell first |

| `no such column: name` | Column name typo | Run `PRAGMA table\_info(students)` |

| `ProgrammingError: closed connection` | conn was closed | Re-run `conn = sqlite3.connect('college.db')` |

| Chart appears blank | DataFrame is empty | `print(result\_df)` before charting |

| Labels cut off | `tight\_layout()` missing | Add `plt.tight\_layout()` before `plt.show()` |



\---



\## Practice Questions



1\. What is the difference between a Primary Key and a Foreign Key?

2\. Write SQL to find the average programming score for female students only.

3\. What is the difference between WHERE and HAVING? Give one example of each.

4\. Write SQL to find departments where average attendance is above 85%.

5\. What does `pd.read\_sql\_query()` return and what arguments does it need?

6\. You want to show how math scores are distributed. Which chart type is best and why?



\---



\## Day 2 Completion Checklist



\- \[ ] `college.db` created from `student\_performance.csv`

\- \[ ] All 8 SQL queries run without errors

\- \[ ] 4 charts created (bar, histogram, grouped bar, pie)

\- \[ ] Student Performance Dashboard (2×2 grid) complete

\- \[ ] Notebook saved as `Day\_02\_Database\_SQL\_Visualization.ipynb`

\- \[ ] Notebook uploaded to GitHub

\- \[ ] Commit message: `Add Day 2: SQL Database + Visualization Dashboard`



\---



\## Day 3 Preview

\*\*ETL Pipelines + Pandas Data Cleaning + Weather API\*\*

\- Call a real API and collect live weather data

\- Clean messy data (nulls, duplicates, wrong types)

\- Build a complete ETL pipeline

\- The student dataset continues!



\---

\*Codeboosters Tech | Data Engineering + GenAI Internship | Day 2 of 10\*

