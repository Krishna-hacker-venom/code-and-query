# PostgreSQL Joins 

## Learning Goal

Understand how SQL JOINs combine data from multiple tables and learn the mindset used by developers when writing JOIN queries.

---

# Database Setup

## Departments Table

```sql
CREATE TABLE departments (
    dept_id INT PRIMARY KEY,
    dept_name VARCHAR(50)
);
```

## Employees Table

```sql
CREATE TABLE employees (
    emp_id INT PRIMARY KEY,
    emp_name VARCHAR(50),
    dept_id INT
);
```

---

# Sample Data

## Departments

```sql
INSERT INTO departments VALUES
(1, 'HR'),
(2, 'IT'),
(3, 'Finance');
```

## Employees

```sql
INSERT INTO employees VALUES
(101, 'Krishna', 1),
(102, 'Amit', 2),
(103, 'Neha', 2),
(104, 'Arjun', NULL);
```

---

# Important Concepts

## Why JOINs Exist

Data is usually stored in multiple tables.

Employees:

| emp_id | emp_name | dept_id |
| ------ | -------- | ------- |
| 101    | Krishna  | 1       |
| 102    | Amit     | 2       |

Departments:

| dept_id | dept_name |
| ------- | --------- |
| 1       | HR        |
| 2       | IT        |

To see:

```text
Krishna -> HR
Amit -> IT
```

We must combine the tables using JOIN.

---

# INNER JOIN

## Syntax

```sql
SELECT
    e.emp_name,
    d.dept_name
FROM employees e
INNER JOIN departments d
ON e.dept_id = d.dept_id;
```

## Mental Model

```text
Take employees table
+
Take departments table
+
Keep only matching department IDs
```

## Result

| emp_name | dept_name |
| -------- | --------- |
| Krishna  | HR        |
| Amit     | IT        |
| Neha     | IT        |

Rows without matches are removed.

---

# INNER JOIN Breakdown

```sql
FROM employees e
```

Start with employees.

```sql
INNER JOIN departments d
```

Bring departments table.

```sql
ON e.dept_id = d.dept_id
```

Match rows where IDs are equal.

---

# Show All Columns

```sql
SELECT *
FROM employees e
INNER JOIN departments d
ON e.dept_id = d.dept_id;
```

This returns columns from both tables.

---

# LEFT JOIN

## Syntax

```sql
SELECT
    e.emp_name,
    d.dept_name
FROM employees e
LEFT JOIN departments d
ON e.dept_id = d.dept_id;
```

## Mental Model

```text
Keep ALL rows from the left table.
```

Even if no department matches, the employee still appears.

---

# Developer Thinking Process

Whenever writing a JOIN, ask:

### Question 1

What is my starting table?

```sql
FROM employees
```

### Question 2

What table am I bringing in?

```sql
JOIN departments
```

### Question 3

What columns connect them?

```sql
employees.dept_id = departments.dept_id
```

### Question 4

What rows should survive?

* Matching only → INNER JOIN
* All left rows → LEFT JOIN
* All right rows → RIGHT JOIN
* Everything → FULL JOIN

---

# Mistakes I Made

## Mistake 1: Using Double Quotes for Strings

Wrong:

```sql
INSERT INTO employees VALUES
(101,"krishna",1);
```

Error:

```text
column "krishna" does not exist
```

Reason:

PostgreSQL treats double quotes as identifiers (column names).

Correct:

```sql
INSERT INTO employees VALUES
(101,'krishna',1);
```

Rule:

```text
'text'  -> string value
"text"  -> identifier/column/table name
```

---

## Mistake 2: Typo in Column Name

Wrong:

```sql
SELECT e.emp_name, d.ddept_name
FROM employees e
INNER JOIN departments d
ON e.dept_id = d.dept_id;
```

Error:

```text
column d.ddept_name does not exist
```

Correct:

```sql
SELECT e.emp_name, d.dept_name
FROM employees e
INNER JOIN departments d
ON e.dept_id = d.dept_id;
```

Lesson:

Always verify column names using:

```sql
\d departments
```

or

```sql
SELECT * FROM departments;
```

---

# Commands Frequently Used

## Show Tables

```sql
\dt
```

## Describe Table

```sql
\d employees
```

```sql
\d departments
```

## View Data

```sql
SELECT * FROM employees;
```

```sql
SELECT * FROM departments;
```

---

