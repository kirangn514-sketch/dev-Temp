# SQL Interview Topics: Basic to Advanced

A comprehensive guide covering all major SQL concepts for interview preparation.

---

## Table of Contents

1. [Basic SQL Concepts](#1-basic-sql-concepts)
2. [Data Types](#2-data-types)
3. [DDL – Data Definition Language](#3-ddl--data-definition-language)
4. [DML – Data Manipulation Language](#4-dml--data-manipulation-language)
5. [DQL – Data Query Language](#5-dql--data-query-language)
6. [Filtering & Sorting](#6-filtering--sorting)
7. [Aggregate Functions](#7-aggregate-functions)
8. [GROUP BY & HAVING](#8-group-by--having)
9. [Joins](#9-joins)
10. [Subqueries](#10-subqueries)
11. [Set Operators](#11-set-operators)
12. [Constraints](#12-constraints)
13. [Indexes](#13-indexes)
14. [Views](#14-views)
15. [Stored Procedures & Functions](#15-stored-procedures--functions)
16. [Triggers](#16-triggers)
17. [Transactions & ACID Properties](#17-transactions--acid-properties)
18. [Window Functions](#18-window-functions)
19. [CTEs (Common Table Expressions)](#19-ctes-common-table-expressions)
20. [Normalization](#20-normalization)
21. [Query Optimization & Performance](#21-query-optimization--performance)
22. [Advanced Topics](#22-advanced-topics)

---

## 1. Basic SQL Concepts

### What is SQL?
SQL (Structured Query Language) is a standard language used to communicate with relational databases. It allows you to create, read, update, and delete data.

### Types of SQL Commands

| Category | Full Form | Purpose | Commands |
|----------|-----------|---------|----------|
| DDL | Data Definition Language | Define structure | CREATE, ALTER, DROP, TRUNCATE |
| DML | Data Manipulation Language | Manipulate data | INSERT, UPDATE, DELETE |
| DQL | Data Query Language | Query data | SELECT |
| DCL | Data Control Language | Control access | GRANT, REVOKE |
| TCL | Transaction Control Language | Manage transactions | COMMIT, ROLLBACK, SAVEPOINT |

### What is a Relational Database?
A relational database stores data in tables (relations) consisting of rows (records/tuples) and columns (attributes/fields). Tables are related to each other through keys.

---

## 2. Data Types

### Numeric Types
```sql
INT           -- Integer: -2,147,483,648 to 2,147,483,647
BIGINT        -- Large integer
SMALLINT      -- Small integer
DECIMAL(p,s)  -- Exact numeric with precision and scale
FLOAT         -- Approximate floating point
```

### String Types
```sql
CHAR(n)       -- Fixed-length string (padded with spaces)
VARCHAR(n)    -- Variable-length string (up to n characters)
TEXT          -- Large variable-length string
```

### Date/Time Types
```sql
DATE          -- Stores date only: YYYY-MM-DD
TIME          -- Stores time only: HH:MM:SS
DATETIME      -- Date and time combined
TIMESTAMP     -- Stores timestamp, auto-updates on change
```

### Other Types
```sql
BOOLEAN       -- TRUE or FALSE
BLOB          -- Binary Large Object (images, files)
ENUM          -- A set of predefined string values
```

---

## 3. DDL – Data Definition Language

### CREATE TABLE
Used to create a new table in the database.

```sql
CREATE TABLE employees (
    emp_id    INT PRIMARY KEY,
    name      VARCHAR(100) NOT NULL,
    email     VARCHAR(150) UNIQUE,
    salary    DECIMAL(10, 2),
    dept_id   INT,
    hire_date DATE DEFAULT CURRENT_DATE
);
```

### ALTER TABLE
Used to modify an existing table structure.

```sql
-- Add a new column
ALTER TABLE employees ADD COLUMN phone VARCHAR(15);

-- Modify column data type
ALTER TABLE employees MODIFY COLUMN salary DECIMAL(12, 2);

-- Drop a column
ALTER TABLE employees DROP COLUMN phone;

-- Rename a column
ALTER TABLE employees RENAME COLUMN name TO full_name;
```

### DROP vs TRUNCATE vs DELETE

| Command | Removes Data | Removes Structure | Can Rollback | Resets Auto-Increment |
|---------|-------------|-------------------|--------------|----------------------|
| DELETE  | Yes (rows)  | No                | Yes          | No                   |
| TRUNCATE| Yes (all rows)| No              | No (mostly)  | Yes                  |
| DROP    | Yes         | Yes               | No           | Yes (table gone)     |

```sql
DELETE FROM employees WHERE emp_id = 5;   -- Removes specific rows
TRUNCATE TABLE employees;                  -- Removes all rows, keeps structure
DROP TABLE employees;                      -- Removes table entirely
```

---

## 4. DML – Data Manipulation Language

### INSERT
```sql
-- Insert single row
INSERT INTO employees (emp_id, name, email, salary)
VALUES (1, 'Alice', 'alice@example.com', 75000.00);

-- Insert multiple rows
INSERT INTO employees (emp_id, name, salary)
VALUES
    (2, 'Bob', 65000),
    (3, 'Charlie', 80000);

-- Insert from another table
INSERT INTO archived_employees
SELECT * FROM employees WHERE hire_date < '2020-01-01';
```

### UPDATE
```sql
-- Update specific rows
UPDATE employees
SET salary = salary * 1.10
WHERE dept_id = 3;

-- Update with JOIN
UPDATE employees e
JOIN departments d ON e.dept_id = d.dept_id
SET e.salary = e.salary * 1.15
WHERE d.dept_name = 'Engineering';
```

### DELETE
```sql
-- Delete specific rows
DELETE FROM employees WHERE emp_id = 5;

-- Delete with subquery
DELETE FROM employees
WHERE dept_id IN (SELECT dept_id FROM departments WHERE location = 'Mumbai');
```

---

## 5. DQL – Data Query Language

### SELECT Statement
The SELECT statement is the most commonly used SQL command.

```sql
-- Basic select
SELECT * FROM employees;

-- Select specific columns
SELECT emp_id, name, salary FROM employees;

-- Using aliases
SELECT emp_id AS "Employee ID", name AS "Full Name" FROM employees;

-- Select with expressions
SELECT name, salary, salary * 0.20 AS tax FROM employees;
```

### DISTINCT
Removes duplicate rows from the result set.

```sql
SELECT DISTINCT dept_id FROM employees;

-- Count distinct values
SELECT COUNT(DISTINCT dept_id) AS unique_depts FROM employees;
```

---

## 6. Filtering & Sorting

### WHERE Clause
Filters rows based on a condition.

```sql
-- Comparison operators
SELECT * FROM employees WHERE salary > 50000;
SELECT * FROM employees WHERE dept_id != 3;

-- Logical operators: AND, OR, NOT
SELECT * FROM employees WHERE salary > 60000 AND dept_id = 2;
SELECT * FROM employees WHERE dept_id = 1 OR dept_id = 3;
SELECT * FROM employees WHERE NOT dept_id = 5;

-- BETWEEN (inclusive)
SELECT * FROM employees WHERE salary BETWEEN 40000 AND 80000;

-- IN operator
SELECT * FROM employees WHERE dept_id IN (1, 2, 4);

-- LIKE operator (pattern matching)
SELECT * FROM employees WHERE name LIKE 'A%';    -- Starts with A
SELECT * FROM employees WHERE name LIKE '%son';  -- Ends with son
SELECT * FROM employees WHERE name LIKE '_a%';   -- Second letter is 'a'

-- IS NULL / IS NOT NULL
SELECT * FROM employees WHERE email IS NULL;
SELECT * FROM employees WHERE email IS NOT NULL;
```

### ORDER BY
Sorts the result set.

```sql
-- Ascending (default)
SELECT * FROM employees ORDER BY salary ASC;

-- Descending
SELECT * FROM employees ORDER BY salary DESC;

-- Multiple columns
SELECT * FROM employees ORDER BY dept_id ASC, salary DESC;
```

### LIMIT / TOP / FETCH
Restricts number of rows returned.

```sql
-- MySQL / PostgreSQL
SELECT * FROM employees ORDER BY salary DESC LIMIT 10;

-- SQL Server
SELECT TOP 10 * FROM employees ORDER BY salary DESC;

-- Standard SQL (ANSI)
SELECT * FROM employees ORDER BY salary DESC FETCH FIRST 10 ROWS ONLY;
```

---

## 7. Aggregate Functions

Aggregate functions perform calculations on a set of values and return a single value.

```sql
SELECT
    COUNT(*)            AS total_employees,
    COUNT(email)        AS employees_with_email,  -- Ignores NULLs
    SUM(salary)         AS total_salary,
    AVG(salary)         AS average_salary,
    MIN(salary)         AS minimum_salary,
    MAX(salary)         AS maximum_salary
FROM employees;
```

### NULL Handling in Aggregates
- `COUNT(*)` counts all rows including NULLs
- `COUNT(column)` ignores NULLs
- `SUM`, `AVG`, `MIN`, `MAX` all ignore NULL values

```sql
-- Replace NULL with 0 before aggregating
SELECT AVG(COALESCE(bonus, 0)) FROM employees;
```

---

## 8. GROUP BY & HAVING

### GROUP BY
Groups rows that share the same value in a specified column.

```sql
-- Total salary per department
SELECT dept_id, COUNT(*) AS headcount, SUM(salary) AS total_salary
FROM employees
GROUP BY dept_id;

-- Group by multiple columns
SELECT dept_id, job_title, AVG(salary) AS avg_salary
FROM employees
GROUP BY dept_id, job_title;
```

### HAVING
Filters groups after GROUP BY (like WHERE but for groups).

```sql
-- Departments with more than 5 employees
SELECT dept_id, COUNT(*) AS headcount
FROM employees
GROUP BY dept_id
HAVING COUNT(*) > 5;

-- Combined WHERE and HAVING
SELECT dept_id, AVG(salary) AS avg_salary
FROM employees
WHERE hire_date > '2020-01-01'   -- Filters rows BEFORE grouping
GROUP BY dept_id
HAVING AVG(salary) > 60000;      -- Filters groups AFTER grouping
```

### Execution Order of a SQL Query
```
FROM → JOIN → WHERE → GROUP BY → HAVING → SELECT → DISTINCT → ORDER BY → LIMIT
```

---

## 9. Joins

Joins combine rows from two or more tables based on a related column.

### INNER JOIN
Returns only rows that have matching values in both tables.

```sql
SELECT e.name, d.dept_name
FROM employees e
INNER JOIN departments d ON e.dept_id = d.dept_id;
```

### LEFT JOIN (LEFT OUTER JOIN)
Returns all rows from the left table, and matching rows from the right. Non-matching right rows return NULL.

```sql
SELECT e.name, d.dept_name
FROM employees e
LEFT JOIN departments d ON e.dept_id = d.dept_id;
-- Employees with no department will still appear (dept_name = NULL)
```

### RIGHT JOIN (RIGHT OUTER JOIN)
Returns all rows from the right table, and matching rows from the left.

```sql
SELECT e.name, d.dept_name
FROM employees e
RIGHT JOIN departments d ON e.dept_id = d.dept_id;
-- All departments appear, even those with no employees
```

### FULL OUTER JOIN
Returns all rows from both tables, with NULLs where there is no match.

```sql
SELECT e.name, d.dept_name
FROM employees e
FULL OUTER JOIN departments d ON e.dept_id = d.dept_id;
```

### CROSS JOIN
Returns the Cartesian product of two tables (every row from table A × every row from table B).

```sql
SELECT e.name, d.dept_name
FROM employees e
CROSS JOIN departments d;
-- If employees has 10 rows and departments has 5 rows → 50 result rows
```

### SELF JOIN
A table joined with itself. Used for hierarchical data.

```sql
-- Find employee and their manager
SELECT e.name AS employee, m.name AS manager
FROM employees e
LEFT JOIN employees m ON e.manager_id = m.emp_id;
```

### Join Diagram Summary

```
LEFT JOIN:     [LEFT TABLE ██████████| matching |]
INNER JOIN:                          [matching]
RIGHT JOIN:                          [matching |██████████ RIGHT TABLE]
FULL JOIN:     [LEFT TABLE ██████████| matching |██████████ RIGHT TABLE]
```

---

## 10. Subqueries

A subquery is a query nested inside another query.

### Subquery in WHERE
```sql
-- Find employees who earn more than the average salary
SELECT name, salary
FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees);
```

### Subquery in FROM (Derived Table / Inline View)
```sql
SELECT dept_id, avg_sal
FROM (
    SELECT dept_id, AVG(salary) AS avg_sal
    FROM employees
    GROUP BY dept_id
) AS dept_avg
WHERE avg_sal > 70000;
```

### Subquery in SELECT (Scalar Subquery)
```sql
SELECT name, salary,
    (SELECT AVG(salary) FROM employees) AS company_avg
FROM employees;
```

### Correlated Subquery
A subquery that references columns from the outer query. Executes once per row.

```sql
-- Find employees who earn more than their department average
SELECT name, salary, dept_id
FROM employees e
WHERE salary > (
    SELECT AVG(salary)
    FROM employees
    WHERE dept_id = e.dept_id   -- References outer query's dept_id
);
```

### EXISTS vs IN

```sql
-- Using IN (fetches all values first)
SELECT * FROM employees
WHERE dept_id IN (SELECT dept_id FROM departments WHERE location = 'Mumbai');

-- Using EXISTS (stops as soon as one match is found — more efficient)
SELECT * FROM employees e
WHERE EXISTS (
    SELECT 1 FROM departments d
    WHERE d.dept_id = e.dept_id AND d.location = 'Mumbai'
);
```

---

## 11. Set Operators

Combine results of two or more SELECT statements. Columns must match in number and data type.

### UNION vs UNION ALL
```sql
-- UNION: Removes duplicates
SELECT name FROM employees_india
UNION
SELECT name FROM employees_usa;

-- UNION ALL: Keeps all rows including duplicates (faster)
SELECT name FROM employees_india
UNION ALL
SELECT name FROM employees_usa;
```

### INTERSECT
Returns only rows that appear in BOTH result sets.

```sql
SELECT emp_id FROM project_a
INTERSECT
SELECT emp_id FROM project_b;
-- Employees working on BOTH projects
```

### EXCEPT / MINUS
Returns rows from the first query that do NOT appear in the second.

```sql
SELECT emp_id FROM all_employees
EXCEPT
SELECT emp_id FROM resigned_employees;
-- Employees who have NOT resigned
```

---

## 12. Constraints

Constraints enforce rules on the data in a table.

```sql
CREATE TABLE employees (
    emp_id    INT         PRIMARY KEY,          -- Unique + NOT NULL
    name      VARCHAR(100) NOT NULL,            -- Cannot be NULL
    email     VARCHAR(150) UNIQUE,              -- No duplicate emails
    salary    DECIMAL(10,2) DEFAULT 30000.00,   -- Default value
    age       INT          CHECK (age >= 18),   -- Must satisfy condition
    dept_id   INT          REFERENCES departments(dept_id)  -- Foreign key
);
```

### Primary Key
Uniquely identifies each row. Cannot be NULL. Only one per table.

### Foreign Key
Links one table to another. Ensures referential integrity.

```sql
ALTER TABLE employees
ADD CONSTRAINT fk_dept
FOREIGN KEY (dept_id) REFERENCES departments(dept_id)
ON DELETE CASCADE      -- Delete child rows when parent is deleted
ON UPDATE SET NULL;    -- Set to NULL when parent key changes
```

### Referential Integrity Actions
| Action | Description |
|--------|-------------|
| CASCADE | Propagate change to child rows |
| SET NULL | Set foreign key to NULL |
| SET DEFAULT | Set foreign key to its default value |
| RESTRICT | Prevent the operation |
| NO ACTION | Similar to RESTRICT (checked at end of transaction) |

---

## 13. Indexes

An index is a database object that speeds up data retrieval by creating a lookup structure.

### Creating Indexes
```sql
-- Single column index
CREATE INDEX idx_salary ON employees(salary);

-- Composite index
CREATE INDEX idx_dept_salary ON employees(dept_id, salary);

-- Unique index
CREATE UNIQUE INDEX idx_email ON employees(email);
```

### When to use Indexes
- Columns frequently used in WHERE, JOIN, ORDER BY, GROUP BY
- Columns with high cardinality (many distinct values)
- Large tables where full scans are expensive

### When NOT to use Indexes
- Small tables (full scan is faster)
- Columns that are frequently updated (index must be rebuilt)
- Columns with low cardinality (e.g., gender: M/F)

### Types of Indexes
| Type | Description |
|------|-------------|
| B-Tree Index | Default. Good for equality and range queries |
| Hash Index | Only for equality (=). Not for ranges |
| Clustered Index | Physically sorts data. One per table (usually Primary Key) |
| Non-Clustered Index | Separate structure with pointers to actual rows |
| Covering Index | Index includes all columns needed by query |
| Full-Text Index | For text search (LIKE '%word%') |

---

## 14. Views

A view is a virtual table based on a saved SELECT query. It does not store data itself.

```sql
-- Create a view
CREATE VIEW high_earners AS
SELECT emp_id, name, salary, dept_id
FROM employees
WHERE salary > 80000;

-- Query the view like a table
SELECT * FROM high_earners;

-- Update a view (simple views only)
UPDATE high_earners SET salary = 90000 WHERE emp_id = 3;

-- Drop a view
DROP VIEW high_earners;
```

### Advantages of Views
- Simplify complex queries
- Provide security (expose only certain columns/rows)
- Ensure consistency (centralized query logic)

### Updatable Views
A view is updatable if it:
- Is based on a single table
- Does not use DISTINCT, GROUP BY, HAVING, UNION
- Does not use aggregate functions or subqueries in SELECT

### Materialized Views
Unlike regular views, materialized views store the result physically and need to be refreshed.

```sql
-- PostgreSQL example
CREATE MATERIALIZED VIEW dept_summary AS
SELECT dept_id, COUNT(*) AS headcount, AVG(salary) AS avg_salary
FROM employees GROUP BY dept_id;

REFRESH MATERIALIZED VIEW dept_summary;
```

---

## 15. Stored Procedures & Functions

### Stored Procedure
A precompiled set of SQL statements stored in the database.

```sql
-- MySQL example
DELIMITER //
CREATE PROCEDURE GetEmployeesByDept(IN dept INT)
BEGIN
    SELECT emp_id, name, salary
    FROM employees
    WHERE dept_id = dept;
END //
DELIMITER ;

-- Call the procedure
CALL GetEmployeesByDept(3);
```

### User-Defined Function (UDF)
Returns a single value. Can be used in SELECT.

```sql
-- MySQL example
DELIMITER //
CREATE FUNCTION CalculateBonus(salary DECIMAL(10,2))
RETURNS DECIMAL(10,2) DETERMINISTIC
BEGIN
    RETURN salary * 0.15;
END //
DELIMITER ;

-- Use in SELECT
SELECT name, salary, CalculateBonus(salary) AS bonus FROM employees;
```

### Procedure vs Function

| Feature | Stored Procedure | Function |
|---------|-----------------|----------|
| Returns | Zero or more values | Exactly one value |
| Called via | CALL / EXEC | SELECT / expression |
| DML allowed | Yes | Depends on DB |
| Use in WHERE/SELECT | No | Yes |

---

## 16. Triggers

A trigger is a stored procedure that automatically executes in response to DML events.

```sql
-- Audit trigger: log changes when salary is updated
CREATE TRIGGER trg_salary_update
AFTER UPDATE ON employees
FOR EACH ROW
BEGIN
    IF OLD.salary <> NEW.salary THEN
        INSERT INTO salary_audit (emp_id, old_salary, new_salary, changed_at)
        VALUES (NEW.emp_id, OLD.salary, NEW.salary, NOW());
    END IF;
END;
```

### Trigger Types

| Type | When it fires |
|------|--------------|
| BEFORE INSERT | Before a row is inserted |
| AFTER INSERT | After a row is inserted |
| BEFORE UPDATE | Before a row is updated |
| AFTER UPDATE | After a row is updated |
| BEFORE DELETE | Before a row is deleted |
| AFTER DELETE | After a row is deleted |

### OLD and NEW References
- `OLD.column` — the value before the change
- `NEW.column` — the value after the change

---

## 17. Transactions & ACID Properties

### What is a Transaction?
A transaction is a sequence of SQL operations treated as a single unit of work. Either all operations succeed, or none do.

```sql
START TRANSACTION;

    UPDATE accounts SET balance = balance - 5000 WHERE account_id = 101;  -- Debit
    UPDATE accounts SET balance = balance + 5000 WHERE account_id = 202;  -- Credit

COMMIT;   -- Save changes permanently

-- Or if something goes wrong:
ROLLBACK; -- Undo all changes in the transaction
```

### SAVEPOINT
Partial rollback within a transaction.

```sql
START TRANSACTION;
    INSERT INTO orders VALUES (1, 'Product A', 100);
    SAVEPOINT sp1;

    INSERT INTO orders VALUES (2, 'Product B', 200);
    ROLLBACK TO sp1;  -- Undo only the second insert

COMMIT;  -- Only the first insert is saved
```

### ACID Properties

| Property | Description |
|----------|-------------|
| **Atomicity** | All operations in a transaction succeed or all fail. No partial execution. |
| **Consistency** | Database moves from one valid state to another. All constraints are maintained. |
| **Isolation** | Concurrent transactions do not interfere with each other. |
| **Durability** | Once committed, changes are permanent even after system failure. |

### Isolation Levels

| Isolation Level | Dirty Read | Non-Repeatable Read | Phantom Read |
|----------------|-----------|---------------------|--------------|
| READ UNCOMMITTED | Yes | Yes | Yes |
| READ COMMITTED | No | Yes | Yes |
| REPEATABLE READ | No | No | Yes |
| SERIALIZABLE | No | No | No |

```sql
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;
```

- **Dirty Read**: Reading uncommitted data from another transaction
- **Non-Repeatable Read**: Same row returns different values in the same transaction
- **Phantom Read**: Same query returns different set of rows in the same transaction

---

## 18. Window Functions

Window functions perform calculations across a set of rows related to the current row (the "window"), without collapsing them into a single output row like GROUP BY does.

### Syntax
```sql
function_name() OVER (
    [PARTITION BY column]
    [ORDER BY column]
    [ROWS/RANGE BETWEEN ...]
)
```

### Ranking Functions
```sql
SELECT name, salary, dept_id,
    ROW_NUMBER() OVER (PARTITION BY dept_id ORDER BY salary DESC) AS row_num,
    RANK()       OVER (PARTITION BY dept_id ORDER BY salary DESC) AS rank,
    DENSE_RANK() OVER (PARTITION BY dept_id ORDER BY salary DESC) AS dense_rank,
    NTILE(4)     OVER (ORDER BY salary)                           AS quartile
FROM employees;
```

**Difference between RANK, DENSE_RANK, and ROW_NUMBER:**

| Salary | ROW_NUMBER | RANK | DENSE_RANK |
|--------|-----------|------|------------|
| 90000  | 1         | 1    | 1          |
| 85000  | 2         | 2    | 2          |
| 85000  | 3         | 2    | 2          |
| 75000  | 4         | 4    | 3          |

### Aggregate Window Functions
```sql
SELECT name, salary,
    SUM(salary)   OVER (PARTITION BY dept_id) AS dept_total,
    AVG(salary)   OVER (PARTITION BY dept_id) AS dept_avg,
    COUNT(*)      OVER (PARTITION BY dept_id) AS dept_headcount,
    MAX(salary)   OVER ()                     AS company_max
FROM employees;
```

### Offset Functions
```sql
SELECT name, salary,
    LAG(salary, 1)  OVER (ORDER BY hire_date) AS prev_salary,  -- Previous row
    LEAD(salary, 1) OVER (ORDER BY hire_date) AS next_salary,  -- Next row
    FIRST_VALUE(salary) OVER (PARTITION BY dept_id ORDER BY salary DESC) AS top_salary,
    LAST_VALUE(salary)  OVER (PARTITION BY dept_id ORDER BY salary DESC
                              ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING) AS bottom_salary
FROM employees;
```

### Running Totals (Frames)
```sql
SELECT name, hire_date, salary,
    SUM(salary) OVER (ORDER BY hire_date
                      ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS running_total
FROM employees;
```

---

## 19. CTEs (Common Table Expressions)

A CTE is a named temporary result set defined with the WITH clause. It improves readability and can be referenced multiple times.

### Basic CTE
```sql
WITH dept_avg AS (
    SELECT dept_id, AVG(salary) AS avg_salary
    FROM employees
    GROUP BY dept_id
)
SELECT e.name, e.salary, d.avg_salary
FROM employees e
JOIN dept_avg d ON e.dept_id = d.dept_id
WHERE e.salary > d.avg_salary;
```

### Multiple CTEs
```sql
WITH
    dept_totals AS (
        SELECT dept_id, SUM(salary) AS total
        FROM employees GROUP BY dept_id
    ),
    dept_info AS (
        SELECT d.dept_id, d.dept_name, dt.total
        FROM departments d
        JOIN dept_totals dt ON d.dept_id = dt.dept_id
    )
SELECT * FROM dept_info ORDER BY total DESC;
```

### Recursive CTE
Used to query hierarchical or tree-structured data.

```sql
WITH RECURSIVE org_chart AS (
    -- Anchor member: start with top-level manager
    SELECT emp_id, name, manager_id, 1 AS level
    FROM employees
    WHERE manager_id IS NULL

    UNION ALL

    -- Recursive member: find subordinates
    SELECT e.emp_id, e.name, e.manager_id, oc.level + 1
    FROM employees e
    INNER JOIN org_chart oc ON e.manager_id = oc.emp_id
)
SELECT * FROM org_chart ORDER BY level, name;
```

---

## 20. Normalization

Normalization is the process of organizing a database to reduce data redundancy and improve data integrity.

### First Normal Form (1NF)
- Each column must hold atomic (indivisible) values
- No repeating groups or arrays

```
BAD (Not 1NF):
| emp_id | name  | skills           |
|--------|-------|------------------|
| 1      | Alice | Java, Python, SQL|

GOOD (1NF):
| emp_id | name  | skill  |
|--------|-------|--------|
| 1      | Alice | Java   |
| 1      | Alice | Python |
| 1      | Alice | SQL    |
```

### Second Normal Form (2NF)
- Must be in 1NF
- No partial dependency: every non-key column must depend on the WHOLE primary key (applies to composite keys)

### Third Normal Form (3NF)
- Must be in 2NF
- No transitive dependency: non-key columns must not depend on other non-key columns

### Boyce-Codd Normal Form (BCNF)
- Stricter version of 3NF
- Every determinant must be a candidate key

### Denormalization
Intentional introduction of redundancy to improve read performance (used in data warehouses, reporting databases).

---

## 21. Query Optimization & Performance

### EXPLAIN / EXPLAIN ANALYZE
```sql
EXPLAIN SELECT * FROM employees WHERE dept_id = 3;
EXPLAIN ANALYZE SELECT * FROM employees WHERE dept_id = 3;
```
Examine the query execution plan to detect full table scans, missing indexes, etc.

### Key Optimization Tips

**1. Use Indexes Properly**
```sql
-- GOOD: Index can be used
SELECT * FROM employees WHERE dept_id = 3;

-- BAD: Function on indexed column prevents index use
SELECT * FROM employees WHERE YEAR(hire_date) = 2022;

-- BETTER: Rewrite to use index
SELECT * FROM employees WHERE hire_date BETWEEN '2022-01-01' AND '2022-12-31';
```

**2. Avoid SELECT \***
```sql
-- BAD
SELECT * FROM employees;

-- GOOD: Only fetch what you need
SELECT emp_id, name, salary FROM employees;
```

**3. Use EXISTS instead of IN for large datasets**
```sql
-- GOOD for large subqueries
SELECT * FROM employees e
WHERE EXISTS (SELECT 1 FROM departments d WHERE d.dept_id = e.dept_id AND d.active = 1);
```

**4. Avoid leading wildcards in LIKE**
```sql
-- BAD: Cannot use index
SELECT * FROM employees WHERE name LIKE '%Alice%';

-- GOOD: Can use index
SELECT * FROM employees WHERE name LIKE 'Alice%';
```

**5. Use Covering Indexes**
An index that includes all columns referenced by the query.

```sql
-- Query uses only dept_id and salary
CREATE INDEX idx_covering ON employees(dept_id, salary);
-- The DB can answer the query purely from the index without touching the actual table
```

**6. Avoid OR in WHERE (use UNION instead)**
```sql
-- May not use index efficiently
SELECT * FROM employees WHERE dept_id = 1 OR dept_id = 2;

-- Better
SELECT * FROM employees WHERE dept_id = 1
UNION ALL
SELECT * FROM employees WHERE dept_id = 2;
```

---

## 22. Advanced Topics

### CASE Expression
Conditional logic inside SQL queries.

```sql
SELECT name, salary,
    CASE
        WHEN salary > 100000 THEN 'High'
        WHEN salary BETWEEN 60000 AND 100000 THEN 'Medium'
        ELSE 'Low'
    END AS salary_band
FROM employees;
```

### COALESCE & NULLIF
```sql
-- COALESCE: Returns first non-NULL value
SELECT name, COALESCE(bonus, 0) AS bonus FROM employees;

-- NULLIF: Returns NULL if two expressions are equal
SELECT NULLIF(salary, 0) FROM employees;  -- Returns NULL if salary = 0
```

### Pivoting Data (CASE + GROUP BY)
```sql
SELECT
    dept_id,
    SUM(CASE WHEN gender = 'M' THEN 1 ELSE 0 END) AS male_count,
    SUM(CASE WHEN gender = 'F' THEN 1 ELSE 0 END) AS female_count
FROM employees
GROUP BY dept_id;
```

### Partitioning
Divides a large table into smaller, manageable pieces stored separately.

```sql
-- Range partitioning (MySQL)
CREATE TABLE orders (
    order_id INT,
    order_date DATE,
    amount DECIMAL(10,2)
)
PARTITION BY RANGE (YEAR(order_date)) (
    PARTITION p2022 VALUES LESS THAN (2023),
    PARTITION p2023 VALUES LESS THAN (2024),
    PARTITION p2024 VALUES LESS THAN (2025)
);
```

### JSON in SQL (Modern Databases)
```sql
-- MySQL/PostgreSQL: Store and query JSON
SELECT
    data->>'$.name' AS name,
    data->>'$.city' AS city
FROM user_profiles
WHERE JSON_EXTRACT(data, '$.age') > 25;
```

### Upsert (INSERT OR UPDATE)
```sql
-- MySQL: INSERT ... ON DUPLICATE KEY UPDATE
INSERT INTO employees (emp_id, name, salary)
VALUES (1, 'Alice', 90000)
ON DUPLICATE KEY UPDATE salary = VALUES(salary);

-- PostgreSQL: INSERT ... ON CONFLICT
INSERT INTO employees (emp_id, name, salary)
VALUES (1, 'Alice', 90000)
ON CONFLICT (emp_id) DO UPDATE SET salary = EXCLUDED.salary;
```

### Gaps and Islands Problem
Find continuous sequences in data.

```sql
-- Find gaps in employee IDs
SELECT a.emp_id + 1 AS gap_start, b.emp_id - 1 AS gap_end
FROM employees a
JOIN employees b ON b.emp_id = (
    SELECT MIN(emp_id) FROM employees WHERE emp_id > a.emp_id
)
WHERE b.emp_id > a.emp_id + 1;
```

### Common Interview Query Patterns

**1. Top N per group (e.g., top 3 earners per department)**
```sql
SELECT name, salary, dept_id
FROM (
    SELECT name, salary, dept_id,
        DENSE_RANK() OVER (PARTITION BY dept_id ORDER BY salary DESC) AS rnk
    FROM employees
) ranked
WHERE rnk <= 3;
```

**2. Find duplicates**
```sql
SELECT email, COUNT(*) AS cnt
FROM employees
GROUP BY email
HAVING COUNT(*) > 1;
```

**3. Delete duplicates (keep one)**
```sql
DELETE FROM employees
WHERE emp_id NOT IN (
    SELECT MIN(emp_id)
    FROM employees
    GROUP BY email
);
```

**4. Second highest salary**
```sql
-- Method 1: Subquery
SELECT MAX(salary) FROM employees
WHERE salary < (SELECT MAX(salary) FROM employees);

-- Method 2: DENSE_RANK
SELECT salary FROM (
    SELECT salary, DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk
    FROM employees
) ranked WHERE rnk = 2 LIMIT 1;
```

**5. Running total / cumulative sum**
```sql
SELECT name, salary,
    SUM(salary) OVER (ORDER BY hire_date) AS running_total
FROM employees;
```

**6. Year-over-year comparison**
```sql
SELECT
    YEAR(order_date) AS yr,
    SUM(amount) AS total,
    LAG(SUM(amount)) OVER (ORDER BY YEAR(order_date)) AS prev_year_total,
    SUM(amount) - LAG(SUM(amount)) OVER (ORDER BY YEAR(order_date)) AS yoy_change
FROM orders
GROUP BY YEAR(order_date);
```

---

## Quick Reference Cheatsheet

### Query Execution Order
```
1. FROM
2. JOIN
3. WHERE
4. GROUP BY
5. HAVING
6. SELECT
7. DISTINCT
8. ORDER BY
9. LIMIT / OFFSET
```

### Joins at a Glance
```
INNER JOIN  → Only matching rows
LEFT JOIN   → All left + matching right (NULLs for non-matching right)
RIGHT JOIN  → All right + matching left (NULLs for non-matching left)
FULL JOIN   → All rows from both (NULLs where no match)
CROSS JOIN  → Every combination (Cartesian product)
SELF JOIN   → Table joined with itself
```

### Window vs Aggregate Functions
```
GROUP BY aggregates    → Collapses rows → one row per group
Window functions       → Keeps all rows → adds calculated column
```

### ACID Reminder
```
A → Atomicity  (All or Nothing)
C → Consistency (Valid state to valid state)
I → Isolation  (Transactions don't interfere)
D → Durability (Committed data is permanent)
```

---

*This guide covers topics from basic SQL syntax through advanced query techniques. Practice each concept with real datasets to build interview confidence.*
