# SQL Interview Questions & Answers

A comprehensive set of interview questions with detailed explanations — Basic to Advanced.

---

## Table of Contents

1. [Basic SQL Questions](#1-basic-sql-questions)
2. [Data Types Questions](#2-data-types-questions)
3. [DDL Questions](#3-ddl-questions)
4. [DML Questions](#4-dml-questions)
5. [SELECT & Filtering Questions](#5-select--filtering-questions)
6. [Aggregate & GROUP BY Questions](#6-aggregate--group-by-questions)
7. [Joins Questions](#7-joins-questions)
8. [Subquery Questions](#8-subquery-questions)
9. [Set Operators Questions](#9-set-operators-questions)
10. [Constraints & Keys Questions](#10-constraints--keys-questions)
11. [Indexes Questions](#11-indexes-questions)
12. [Views Questions](#12-views-questions)
13. [Stored Procedures & Functions Questions](#13-stored-procedures--functions-questions)
14. [Triggers Questions](#14-triggers-questions)
15. [Transactions & ACID Questions](#15-transactions--acid-questions)
16. [Window Functions Questions](#16-window-functions-questions)
17. [CTE Questions](#17-cte-questions)
18. [Normalization Questions](#18-normalization-questions)
19. [Query Optimization Questions](#19-query-optimization-questions)
20. [Advanced & Tricky Questions](#20-advanced--tricky-questions)

---

## 1. Basic SQL Questions

---

### Q1. What is SQL and why is it used?

**Answer:**
SQL (Structured Query Language) is a standard language used to interact with relational databases. It allows you to:
- Create and manage database structures (tables, indexes, views)
- Insert, update, and delete data
- Query and retrieve data
- Control access and manage transactions

It is used because most business data lives in relational databases (MySQL, PostgreSQL, SQL Server, Oracle), and SQL is the universal language to communicate with all of them.

---

### Q2. What are the different types of SQL commands?

**Answer:**

| Category | Full Form | Commands | Purpose |
|----------|-----------|----------|---------|
| DDL | Data Definition Language | CREATE, ALTER, DROP, TRUNCATE | Define/modify structure |
| DML | Data Manipulation Language | INSERT, UPDATE, DELETE | Manipulate data |
| DQL | Data Query Language | SELECT | Query/retrieve data |
| DCL | Data Control Language | GRANT, REVOKE | Control permissions |
| TCL | Transaction Control Language | COMMIT, ROLLBACK, SAVEPOINT | Manage transactions |

---

### Q3. What is the difference between a database and a table?

**Answer:**
- A **database** is a container that holds all the objects — tables, views, stored procedures, indexes, etc.
- A **table** is an object within a database that stores data in rows and columns (like a spreadsheet).

Analogy: A database is like a filing cabinet, and tables are the individual folders inside it.

---

### Q4. What is the difference between CHAR and VARCHAR?

**Answer:**

| Feature | CHAR(n) | VARCHAR(n) |
|---------|---------|------------|
| Length | Fixed length | Variable length |
| Storage | Always uses n bytes | Uses only as many bytes as needed + 1-2 bytes overhead |
| Padding | Pads with spaces if shorter | No padding |
| Performance | Slightly faster for fixed-size data | More efficient for varying-length data |
| Use case | Country codes, gender (M/F) | Names, emails, descriptions |

```sql
CHAR(5)    → 'Hi   '  (padded to 5 characters)
VARCHAR(5) → 'Hi'     (only 2 characters stored)
```

---

### Q5. What is NULL in SQL? How is it different from 0 or empty string?

**Answer:**
`NULL` means the absence of a value — it is unknown or missing. It is NOT the same as:
- `0` — which is a numeric value
- `''` (empty string) — which is a string with zero characters

```sql
SELECT NULL = NULL;   -- Returns NULL (not TRUE!)
SELECT NULL IS NULL;  -- Returns TRUE
SELECT 0 = 0;         -- Returns TRUE
SELECT '' = '';       -- Returns TRUE
```

**Key Rules:**
- Any arithmetic with NULL returns NULL: `5 + NULL = NULL`
- Use `IS NULL` / `IS NOT NULL` to check for NULL (never `= NULL`)
- Aggregate functions like `SUM`, `AVG` ignore NULLs

---

### Q6. What is the difference between DELETE, TRUNCATE, and DROP?

**Answer:**

| Feature | DELETE | TRUNCATE | DROP |
|---------|--------|----------|------|
| What it removes | Specific rows (or all rows) | All rows | Entire table + data |
| WHERE clause | Yes | No | No |
| Rollback possible | Yes | No (in most DBs) | No |
| Auto-increment reset | No | Yes | Yes (table gone) |
| Triggers fired | Yes | No | No |
| Speed | Slower (logs each row) | Faster | Fastest |
| DDL or DML | DML | DDL | DDL |

```sql
DELETE FROM employees WHERE dept_id = 5;  -- Deletes specific rows
TRUNCATE TABLE employees;                  -- Deletes all rows, keeps table
DROP TABLE employees;                      -- Removes the table entirely
```

---

## 2. Data Types Questions

---

### Q7. What is the difference between FLOAT and DECIMAL?

**Answer:**
- `FLOAT` is an approximate numeric type. It uses binary floating point, so values like `0.1` may not be stored exactly. Suitable for scientific calculations where precision is not critical.
- `DECIMAL(p, s)` is an exact numeric type. `p` is total digits, `s` is digits after the decimal point. Used for financial and monetary calculations where precision matters.

```sql
DECIMAL(10, 2) → Stores up to 99999999.99 (exactly)
FLOAT          → May store 0.1 as 0.10000000000000001
```

Always use `DECIMAL` for money.

---

### Q8. What is the difference between DATE, DATETIME, and TIMESTAMP?

**Answer:**

| Type | Format | Range | Time Zone |
|------|--------|-------|-----------|
| DATE | YYYY-MM-DD | 1000-01-01 to 9999-12-31 | No |
| DATETIME | YYYY-MM-DD HH:MM:SS | 1000-01-01 to 9999-12-31 | No |
| TIMESTAMP | YYYY-MM-DD HH:MM:SS | 1970-01-01 to 2038-01-19 | Yes (UTC stored) |

`TIMESTAMP` automatically converts to the server's time zone and is ideal for tracking when a record was created or last updated.

---

## 3. DDL Questions

---

### Q9. What is the difference between ALTER and UPDATE?

**Answer:**
- `ALTER` is a DDL command — it changes the **structure** of a table (add/remove/modify columns, rename table).
- `UPDATE` is a DML command — it changes the **data** inside a table.

```sql
ALTER TABLE employees ADD COLUMN age INT;          -- Adds a new column
UPDATE employees SET salary = 50000 WHERE age < 25; -- Changes data in rows
```

---

### Q10. Can you add a NOT NULL column to an existing table that has data?

**Answer:**
Not directly — if the table already has rows, adding a `NOT NULL` column without a default value will fail because existing rows would have NULL for that column.

**Solution:**
```sql
-- Step 1: Add column allowing NULL
ALTER TABLE employees ADD COLUMN department VARCHAR(50);

-- Step 2: Fill in values for existing rows
UPDATE employees SET department = 'General';

-- Step 3: Now apply NOT NULL constraint
ALTER TABLE employees MODIFY COLUMN department VARCHAR(50) NOT NULL;
```

---

### Q11. What happens when you TRUNCATE a table with a foreign key reference?

**Answer:**
It fails. You cannot TRUNCATE a table that is referenced by a foreign key constraint in another table, because it would break referential integrity.

**Solutions:**
- Disable foreign key checks temporarily (MySQL: `SET FOREIGN_KEY_CHECKS = 0;`)
- DELETE the child records first, then TRUNCATE the parent
- DROP and recreate the foreign key

---

## 4. DML Questions

---

### Q12. What is the difference between INSERT INTO ... VALUES and INSERT INTO ... SELECT?

**Answer:**
- `INSERT INTO ... VALUES` inserts specific literal values you provide.
- `INSERT INTO ... SELECT` inserts rows from the result of a SELECT query — useful for copying or transforming data.

```sql
-- Insert specific values
INSERT INTO employees (name, salary) VALUES ('Alice', 70000);

-- Insert from another table (e.g., archive old employees)
INSERT INTO archived_employees
SELECT * FROM employees WHERE hire_date < '2020-01-01';
```

---

### Q13. How does UPDATE work with JOINs?

**Answer:**
You can UPDATE rows in one table based on values from another table using a JOIN.

```sql
-- Give 10% raise to all employees in the Engineering department
UPDATE employees e
JOIN departments d ON e.dept_id = d.dept_id
SET e.salary = e.salary * 1.10
WHERE d.dept_name = 'Engineering';
```

This is more efficient than using a subquery in some databases.

---

### Q14. How do you copy a table with or without data?

**Answer:**
```sql
-- Copy structure AND data
CREATE TABLE employees_backup AS SELECT * FROM employees;

-- Copy structure ONLY (no data)
CREATE TABLE employees_backup AS SELECT * FROM employees WHERE 1 = 0;
-- The condition 1=0 is always false, so no rows are copied
```

Note: Constraints (PRIMARY KEY, INDEXES) are NOT copied with this method. Use `CREATE TABLE LIKE` (MySQL) or script the DDL separately for full structure copy.

---

## 5. SELECT & Filtering Questions

---

### Q15. What is the order of execution in a SQL SELECT statement?

**Answer:**
SQL is not executed in the order it is written. The actual execution order is:

```
1. FROM        → Identify the source tables
2. JOIN        → Combine tables
3. WHERE       → Filter rows (before grouping)
4. GROUP BY    → Group rows
5. HAVING      → Filter groups (after grouping)
6. SELECT      → Choose columns and expressions
7. DISTINCT    → Remove duplicate rows
8. ORDER BY    → Sort results
9. LIMIT/TOP   → Restrict number of rows
```

This is why you **cannot** use a SELECT alias in a WHERE clause (WHERE is executed before SELECT).

```sql
-- ERROR: alias not yet defined when WHERE runs
SELECT salary * 1.1 AS new_salary FROM employees WHERE new_salary > 60000;

-- CORRECT: use expression directly
SELECT salary * 1.1 AS new_salary FROM employees WHERE salary * 1.1 > 60000;
```

---

### Q16. What is the difference between WHERE and HAVING?

**Answer:**

| Clause | Filters | When it runs | Can use aggregate? |
|--------|---------|-------------|-------------------|
| WHERE  | Individual rows | Before GROUP BY | No |
| HAVING | Groups of rows | After GROUP BY | Yes |

```sql
SELECT dept_id, AVG(salary) AS avg_sal
FROM employees
WHERE hire_date > '2020-01-01'   -- Filters rows BEFORE grouping
GROUP BY dept_id
HAVING AVG(salary) > 60000;      -- Filters groups AFTER grouping
```

---

### Q17. How does LIKE work? What are wildcards?

**Answer:**
`LIKE` is used for pattern matching on string columns.

| Wildcard | Meaning | Example |
|----------|---------|---------|
| `%` | Zero or more characters | `'A%'` → starts with A |
| `_` | Exactly one character | `'_a%'` → second letter is 'a' |

```sql
SELECT * FROM employees WHERE name LIKE 'A%';       -- Alice, Andrew
SELECT * FROM employees WHERE name LIKE '%son';      -- Johnson, Wilson
SELECT * FROM employees WHERE name LIKE 'A_i%';     -- Alice, Anil
SELECT * FROM employees WHERE email LIKE '%@gmail%'; -- Gmail users
```

**Note:** Leading `%` (e.g., `'%Alice'`) prevents index usage — causes full table scan.

---

### Q18. What is the difference between IN and BETWEEN?

**Answer:**

```sql
-- IN: Matches any value in a list
SELECT * FROM employees WHERE dept_id IN (1, 3, 5);

-- BETWEEN: Matches values in a range (inclusive on both ends)
SELECT * FROM employees WHERE salary BETWEEN 40000 AND 80000;
-- Equivalent to: salary >= 40000 AND salary <= 80000
```

`BETWEEN` works on numbers, dates, and strings (alphabetical order for strings).

---

## 6. Aggregate & GROUP BY Questions

---

### Q19. What is the difference between COUNT(*), COUNT(column), and COUNT(DISTINCT column)?

**Answer:**

```sql
SELECT
    COUNT(*)                 AS total_rows,        -- Counts ALL rows, including NULLs
    COUNT(email)             AS non_null_emails,   -- Counts rows where email IS NOT NULL
    COUNT(DISTINCT dept_id)  AS unique_departments -- Counts unique non-NULL dept_id values
FROM employees;
```

If `email` is NULL for 3 employees:
- `COUNT(*)` → 100 (all rows)
- `COUNT(email)` → 97 (excludes NULLs)
- `COUNT(DISTINCT dept_id)` → number of unique departments

---

### Q20. Can you use an aggregate function in a WHERE clause?

**Answer:**
No. Aggregate functions cannot be used in WHERE because WHERE runs before grouping/aggregation. Use HAVING instead.

```sql
-- WRONG
SELECT dept_id FROM employees WHERE AVG(salary) > 60000;

-- CORRECT
SELECT dept_id, AVG(salary) AS avg_sal
FROM employees
GROUP BY dept_id
HAVING AVG(salary) > 60000;
```

---

### Q21. What rules must you follow when using GROUP BY?

**Answer:**
Every column in the SELECT list must either:
1. Be included in the GROUP BY clause, OR
2. Be wrapped in an aggregate function (SUM, COUNT, AVG, MIN, MAX)

```sql
-- WRONG: name is not in GROUP BY and not aggregated
SELECT dept_id, name, COUNT(*) FROM employees GROUP BY dept_id;

-- CORRECT
SELECT dept_id, COUNT(*) AS headcount FROM employees GROUP BY dept_id;

-- CORRECT: name is also grouped
SELECT dept_id, name, COUNT(*) FROM employees GROUP BY dept_id, name;
```

---

## 7. Joins Questions

---

### Q22. What is the difference between INNER JOIN and LEFT JOIN?

**Answer:**

```sql
-- INNER JOIN: Only rows with a match in BOTH tables
SELECT e.name, d.dept_name
FROM employees e
INNER JOIN departments d ON e.dept_id = d.dept_id;
-- Employees without a department are EXCLUDED

-- LEFT JOIN: ALL rows from left table + matching from right
SELECT e.name, d.dept_name
FROM employees e
LEFT JOIN departments d ON e.dept_id = d.dept_id;
-- Employees without a department are INCLUDED (dept_name = NULL)
```

Use LEFT JOIN when you want to preserve all records from the left (primary) table even if there's no match in the right.

---

### Q23. How do you find rows that exist in one table but not another?

**Answer:**
Use a LEFT JOIN and filter for NULLs in the right table (anti-join pattern).

```sql
-- Find employees who are NOT assigned to any department
SELECT e.emp_id, e.name
FROM employees e
LEFT JOIN departments d ON e.dept_id = d.dept_id
WHERE d.dept_id IS NULL;
```

Alternative using NOT EXISTS:
```sql
SELECT emp_id, name FROM employees e
WHERE NOT EXISTS (
    SELECT 1 FROM departments d WHERE d.dept_id = e.dept_id
);
```

---

### Q24. What is a SELF JOIN and when would you use it?

**Answer:**
A SELF JOIN joins a table to itself. It is used when a table contains a hierarchical or recursive relationship (like employee-manager).

```sql
-- Employee and their manager (both stored in the same table)
SELECT
    e.name  AS employee,
    m.name  AS manager
FROM employees e
LEFT JOIN employees m ON e.manager_id = m.emp_id;
```

The table is aliased twice (`e` for employee, `m` for manager) to distinguish the two roles.

---

### Q25. What is a CROSS JOIN? When would you use it?

**Answer:**
A CROSS JOIN returns the Cartesian product — every row from Table A combined with every row from Table B.

```sql
SELECT p.product_name, c.color
FROM products p
CROSS JOIN colors c;
-- If products has 5 rows and colors has 4 rows → 20 result rows
```

**Use cases:**
- Generate all possible combinations (e.g., product variants, schedule slots)
- Create test/seed data

Be careful — CROSS JOIN on large tables produces massive result sets.

---

### Q26. What is the difference between ON and USING in joins?

**Answer:**
- `ON` — specifies the join condition explicitly; columns can have different names
- `USING` — shorthand when both tables have the same column name; the column appears once in the result

```sql
-- ON: more flexible
SELECT e.name, d.dept_name
FROM employees e JOIN departments d ON e.dept_id = d.dept_id;

-- USING: only when column name is the same in both tables
SELECT name, dept_name
FROM employees JOIN departments USING (dept_id);
```

---

## 8. Subquery Questions

---

### Q27. What is a correlated subquery? How is it different from a regular subquery?

**Answer:**

A **regular subquery** runs once and its result is passed to the outer query.

```sql
SELECT name FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees);
-- Inner query runs ONCE
```

A **correlated subquery** references a column from the outer query, so it runs once **per row** of the outer query.

```sql
SELECT name, salary, dept_id FROM employees e
WHERE salary > (
    SELECT AVG(salary) FROM employees
    WHERE dept_id = e.dept_id   -- References outer query's row
);
-- Inner query runs ONCE PER ROW in the outer query
```

Correlated subqueries are more powerful but can be slower on large datasets. Often replaceable with window functions.

---

### Q28. What is the difference between IN, EXISTS, and JOIN for filtering?

**Answer:**

```sql
-- IN: Fetches all matching values first, then filters
SELECT * FROM employees
WHERE dept_id IN (SELECT dept_id FROM departments WHERE location = 'Mumbai');

-- EXISTS: Checks for existence only — stops at first match (more efficient)
SELECT * FROM employees e
WHERE EXISTS (
    SELECT 1 FROM departments d
    WHERE d.dept_id = e.dept_id AND d.location = 'Mumbai'
);

-- JOIN: Usually most efficient when you also need columns from both tables
SELECT e.*
FROM employees e
JOIN departments d ON e.dept_id = d.dept_id AND d.location = 'Mumbai';
```

**Rule of thumb:**
- Use `EXISTS` over `IN` for large subqueries
- Use `JOIN` when you need data from both tables
- Use `NOT EXISTS` over `NOT IN` when NULLs may be present (NOT IN with NULLs returns no rows)

---

### Q29. What is a derived table / inline view?

**Answer:**
A derived table is a subquery used in the FROM clause. It acts like a temporary table only for that query.

```sql
SELECT dept_id, avg_sal
FROM (
    SELECT dept_id, AVG(salary) AS avg_sal
    FROM employees
    GROUP BY dept_id
) AS dept_averages           -- Must be aliased
WHERE avg_sal > 65000;
```

The inner query runs first and its result is used as a "virtual table" by the outer query. CTEs are often preferred over derived tables for readability.

---

## 9. Set Operators Questions

---

### Q30. What is the difference between UNION and UNION ALL?

**Answer:**

```sql
-- UNION: Combines results and removes duplicates
SELECT name FROM employees_india
UNION
SELECT name FROM employees_usa;

-- UNION ALL: Combines results and keeps ALL rows including duplicates (faster)
SELECT name FROM employees_india
UNION ALL
SELECT name FROM employees_usa;
```

| Feature | UNION | UNION ALL |
|---------|-------|-----------|
| Duplicates | Removed | Kept |
| Performance | Slower (sorts to deduplicate) | Faster |
| Use when | Unique results needed | Speed matters / duplicates expected |

**Rules for both:** Same number of columns, compatible data types.

---

### Q31. What is INTERSECT and EXCEPT?

**Answer:**

```sql
-- INTERSECT: Rows that appear in BOTH results
SELECT emp_id FROM project_alpha
INTERSECT
SELECT emp_id FROM project_beta;
-- Employees working on BOTH projects

-- EXCEPT (MINUS in Oracle): Rows in first result NOT in second
SELECT emp_id FROM all_employees
EXCEPT
SELECT emp_id FROM resigned_employees;
-- Active employees only
```

---

## 10. Constraints & Keys Questions

---

### Q32. What is the difference between Primary Key and Unique Key?

**Answer:**

| Feature | Primary Key | Unique Key |
|---------|-------------|------------|
| NULL values | Not allowed | Allowed (one NULL in most DBs) |
| Count per table | Only ONE | Multiple allowed |
| Clustered index | Usually creates one | Creates non-clustered index |
| Purpose | Uniquely identifies each row | Ensures no duplicate values |

```sql
CREATE TABLE employees (
    emp_id  INT PRIMARY KEY,             -- Cannot be NULL, only one
    email   VARCHAR(150) UNIQUE,         -- Can be NULL, but no duplicate non-NULL values
    pan_no  VARCHAR(10) UNIQUE           -- Another unique constraint
);
```

---

### Q33. What is a Foreign Key? What is referential integrity?

**Answer:**
A **Foreign Key** is a column in one table that references the Primary Key of another table. It creates a link between the two tables.

**Referential integrity** means the database enforces that a foreign key value must either:
- Match an existing value in the parent table, OR
- Be NULL (if allowed)

```sql
CREATE TABLE orders (
    order_id  INT PRIMARY KEY,
    emp_id    INT,
    FOREIGN KEY (emp_id) REFERENCES employees(emp_id)
    ON DELETE CASCADE   -- If employee is deleted, their orders are also deleted
);
```

Without foreign keys, you could have orders referencing employees that don't exist (orphan records).

---

### Q34. What is the difference between ON DELETE CASCADE, SET NULL, and RESTRICT?

**Answer:**

| Action | What happens to child rows when parent is deleted |
|--------|--------------------------------------------------|
| CASCADE | Child rows are also deleted automatically |
| SET NULL | Foreign key in child is set to NULL |
| SET DEFAULT | Foreign key in child is set to its default value |
| RESTRICT | Deletion is prevented if child rows exist |
| NO ACTION | Similar to RESTRICT (checked at end of transaction) |

```sql
-- Employee deleted → all their orders deleted too
FOREIGN KEY (emp_id) REFERENCES employees(emp_id) ON DELETE CASCADE;

-- Employee deleted → orders remain but emp_id becomes NULL
FOREIGN KEY (emp_id) REFERENCES employees(emp_id) ON DELETE SET NULL;
```

---

### Q35. What is a composite key?

**Answer:**
A composite key is a primary key made up of two or more columns. Together, they uniquely identify a row.

```sql
CREATE TABLE order_items (
    order_id    INT,
    product_id  INT,
    quantity    INT,
    PRIMARY KEY (order_id, product_id)  -- Composite primary key
);
```

Neither `order_id` nor `product_id` alone is unique, but the combination of both is.

---

## 11. Indexes Questions

---

### Q36. What is an index? How does it improve performance?

**Answer:**
An index is a separate data structure (usually a B-Tree) that the database maintains to allow fast lookup of rows based on column values — similar to the index at the back of a book.

Without an index → full table scan (reads every row).
With an index → direct lookup (jumps to relevant rows).

```sql
CREATE INDEX idx_salary ON employees(salary);
-- Now queries like WHERE salary > 80000 are much faster
```

**Trade-off:** Indexes speed up reads but slow down writes (INSERT/UPDATE/DELETE must also update the index).

---

### Q37. What is the difference between a clustered and non-clustered index?

**Answer:**

| Feature | Clustered Index | Non-Clustered Index |
|---------|----------------|---------------------|
| Data storage | Data rows stored in index order | Separate structure; contains pointers to rows |
| Count per table | Only ONE | Multiple allowed |
| Lookup | Very fast for range queries | Fast for point lookups; extra pointer step |
| Default | Usually Primary Key | All other indexes |

```sql
-- Clustered: rows physically sorted by emp_id (usually automatic for PK)
PRIMARY KEY (emp_id)

-- Non-clustered: separate index pointing to rows
CREATE INDEX idx_email ON employees(email);
```

---

### Q38. When should you NOT use an index?

**Answer:**
Avoid indexes when:
- The table is small (full scan is faster than index overhead)
- The column has very low cardinality (e.g., a gender column with only M/F — index is not selective enough)
- The column is frequently updated (index must be rebuilt on every update)
- The query uses functions on the indexed column (`WHERE YEAR(hire_date) = 2022` prevents index use)
- Bulk INSERT/UPDATE/DELETE operations — disable indexes first, then rebuild

---

## 12. Views Questions

---

### Q39. What is a View? What are its advantages?

**Answer:**
A view is a stored SELECT query that acts like a virtual table. It does not store data itself — it runs the underlying query each time it is accessed.

```sql
CREATE VIEW senior_employees AS
SELECT emp_id, name, salary, dept_id
FROM employees
WHERE salary > 80000 AND hire_date < '2018-01-01';

SELECT * FROM senior_employees;  -- Acts like a table
```

**Advantages:**
- **Simplicity:** Hide complex joins/logic behind a simple name
- **Security:** Expose only certain columns/rows to specific users
- **Consistency:** Central definition — one change updates all references
- **Maintainability:** Business logic in one place

---

### Q40. What is the difference between a View and a Materialized View?

**Answer:**

| Feature | View | Materialized View |
|---------|------|------------------|
| Data storage | No (runs query every time) | Yes (stores result physically) |
| Speed | Slower (query runs on access) | Faster (precomputed result) |
| Freshness | Always current | Can be stale (needs refresh) |
| Storage | No extra storage | Uses disk space |
| Use case | Real-time data needs | Reporting, heavy aggregations |

```sql
-- Materialized View (PostgreSQL)
CREATE MATERIALIZED VIEW dept_summary AS
SELECT dept_id, COUNT(*) AS headcount, AVG(salary) AS avg_salary
FROM employees GROUP BY dept_id;

REFRESH MATERIALIZED VIEW dept_summary;  -- Manually refresh when needed
```

---

### Q41. Can you UPDATE data through a View?

**Answer:**
Only simple views are updatable. A view is updatable if it:
- Is based on a single table
- Does not use DISTINCT, GROUP BY, HAVING, UNION
- Does not use aggregate functions or subqueries in SELECT
- Includes the primary key of the base table

```sql
-- This view IS updatable
CREATE VIEW emp_names AS SELECT emp_id, name FROM employees;
UPDATE emp_names SET name = 'Bob' WHERE emp_id = 5;  -- Works

-- This view is NOT updatable (uses GROUP BY + aggregate)
CREATE VIEW dept_stats AS SELECT dept_id, COUNT(*) FROM employees GROUP BY dept_id;
UPDATE dept_stats SET ...  -- ERROR
```

---

## 13. Stored Procedures & Functions Questions

---

### Q42. What is the difference between a Stored Procedure and a Function?

**Answer:**

| Feature | Stored Procedure | Function |
|---------|-----------------|----------|
| Return value | Zero, one, or many (via OUT params) | Must return exactly one value |
| Called with | CALL / EXEC | Used in SELECT or expressions |
| Use in SQL expressions | No | Yes |
| DML operations | Yes | Depends on DB (usually read-only in functions) |
| Transaction control | Yes | No (usually) |
| Purpose | Business logic, multi-step operations | Computations, transformations |

```sql
-- Procedure: called standalone
CALL GetEmployeesByDept(3);

-- Function: used in SELECT
SELECT name, CalculateBonus(salary) AS bonus FROM employees;
```

---

### Q43. What are the advantages of Stored Procedures?

**Answer:**
- **Performance:** Precompiled and cached — runs faster than ad-hoc queries
- **Security:** Grant access to procedure without exposing underlying tables
- **Reusability:** Write once, call many times from different applications
- **Reduced network traffic:** Send one call instead of many SQL statements
- **Maintainability:** Business logic centralized in the database

---

## 14. Triggers Questions

---

### Q44. What is a Trigger? What are the different types?

**Answer:**
A trigger is a stored block of SQL code that automatically executes in response to a specified event on a table.

**Types by Timing:**
- `BEFORE` — runs before the event (can modify `NEW` values)
- `AFTER` — runs after the event (used for auditing)

**Types by Event:**
- `INSERT` — when a row is inserted
- `UPDATE` — when a row is updated
- `DELETE` — when a row is deleted

```sql
-- AFTER UPDATE trigger for audit logging
CREATE TRIGGER log_salary_change
AFTER UPDATE ON employees
FOR EACH ROW
BEGIN
    IF OLD.salary <> NEW.salary THEN
        INSERT INTO audit_log (emp_id, old_salary, new_salary, changed_at)
        VALUES (NEW.emp_id, OLD.salary, NEW.salary, NOW());
    END IF;
END;
```

---

### Q45. What is the difference between OLD and NEW in triggers?

**Answer:**
- `OLD` refers to the row data **before** the change
- `NEW` refers to the row data **after** the change

| Operation | OLD available | NEW available |
|-----------|--------------|--------------|
| INSERT | No | Yes |
| UPDATE | Yes | Yes |
| DELETE | Yes | No |

In a `BEFORE` trigger, you can modify `NEW` values to enforce business rules before the actual write happens.

---

## 15. Transactions & ACID Questions

---

### Q46. What are ACID properties? Explain each.

**Answer:**

**A — Atomicity:** A transaction is all-or-nothing. If any statement fails, the entire transaction is rolled back.
```sql
START TRANSACTION;
UPDATE accounts SET balance = balance - 1000 WHERE id = 1;  -- Debit
UPDATE accounts SET balance = balance + 1000 WHERE id = 2;  -- Credit
COMMIT;  -- Both succeed, or ROLLBACK means neither happens
```

**C — Consistency:** The database moves from one valid state to another. All constraints, rules, and integrity checks are maintained after every transaction.

**I — Isolation:** Concurrent transactions do not interfere with each other. Changes made in one transaction are not visible to others until committed (depending on isolation level).

**D — Durability:** Once a transaction is committed, its changes are permanent — even if the server crashes immediately after.

---

### Q47. What are isolation levels and what problems do they solve?

**Answer:**

**Problems that can occur with concurrent transactions:**
- **Dirty Read:** Transaction reads data that another transaction has modified but not yet committed
- **Non-Repeatable Read:** Same SELECT returns different values within the same transaction (another transaction committed a change between the two reads)
- **Phantom Read:** Same query returns different sets of rows (another transaction inserted/deleted rows between two reads)

**Isolation Levels (from lowest to highest protection):**

| Level | Dirty Read | Non-Repeatable Read | Phantom Read |
|-------|-----------|---------------------|--------------|
| READ UNCOMMITTED | Possible | Possible | Possible |
| READ COMMITTED | Prevented | Possible | Possible |
| REPEATABLE READ | Prevented | Prevented | Possible |
| SERIALIZABLE | Prevented | Prevented | Prevented |

Higher isolation = more protection but lower concurrency and performance.

---

### Q48. What is a deadlock? How can it be prevented?

**Answer:**
A **deadlock** occurs when two or more transactions wait for each other to release locks — neither can proceed.

```
Transaction A: Locks Row 1, waits for Row 2
Transaction B: Locks Row 2, waits for Row 1
→ Deadlock! Neither can proceed.
```

**Prevention strategies:**
- Always access tables/rows in the same order in all transactions
- Keep transactions short and fast
- Use lower isolation levels where acceptable
- Use `SELECT ... FOR UPDATE` carefully
- Implement application-level retry logic for deadlock errors

---

## 16. Window Functions Questions

---

### Q49. What is a Window Function? How is it different from GROUP BY?

**Answer:**

| Feature | GROUP BY + Aggregate | Window Function |
|---------|---------------------|-----------------|
| Output rows | One row per group | Same number of rows as input |
| Access to other rows | No | Yes (window frame) |
| Collapse data | Yes | No |
| Combined with individual rows | Hard | Easy |

```sql
-- GROUP BY: collapses to one row per dept
SELECT dept_id, AVG(salary) FROM employees GROUP BY dept_id;

-- Window: keeps all rows, adds avg column per dept
SELECT name, salary, dept_id,
    AVG(salary) OVER (PARTITION BY dept_id) AS dept_avg
FROM employees;
```

---

### Q50. What is the difference between RANK(), DENSE_RANK(), and ROW_NUMBER()?

**Answer:**

```sql
SELECT name, salary,
    ROW_NUMBER() OVER (ORDER BY salary DESC) AS row_num,
    RANK()       OVER (ORDER BY salary DESC) AS rank,
    DENSE_RANK() OVER (ORDER BY salary DESC) AS dense_rank
FROM employees;
```

| Salary | ROW_NUMBER | RANK | DENSE_RANK |
|--------|-----------|------|------------|
| 90000  | 1         | 1    | 1          |
| 85000  | 2         | 2    | 2          |
| 85000  | 3         | 2    | 2          |
| 70000  | 4         | 4    | 3          |

- `ROW_NUMBER` — always unique, no ties (arbitrary tiebreak)
- `RANK` — ties get same rank, next rank is skipped (gap after ties)
- `DENSE_RANK` — ties get same rank, no gaps in ranking

---

### Q51. How would you find the top 3 highest-paid employees per department?

**Answer:**
Use `DENSE_RANK()` with `PARTITION BY` department.

```sql
SELECT name, salary, dept_id
FROM (
    SELECT name, salary, dept_id,
        DENSE_RANK() OVER (PARTITION BY dept_id ORDER BY salary DESC) AS rnk
    FROM employees
) ranked
WHERE rnk <= 3;
```

`DENSE_RANK` is preferred over `RANK` here because if two employees tie at rank 2, both should be included and rank 3 should still be assigned to the next person.

---

### Q52. What is the difference between LAG and LEAD?

**Answer:**
Both are offset window functions that access values from other rows without a self-join.

```sql
SELECT name, hire_date, salary,
    LAG(salary, 1, 0)  OVER (ORDER BY hire_date) AS prev_employee_salary,  -- Look BACK 1 row
    LEAD(salary, 1, 0) OVER (ORDER BY hire_date) AS next_employee_salary   -- Look AHEAD 1 row
FROM employees;
```

- `LAG(col, n, default)` — returns value from n rows **before** the current row
- `LEAD(col, n, default)` — returns value from n rows **after** the current row
- The third argument is the default value if the offset goes out of bounds

**Common use case:** Calculate month-over-month or year-over-year change.

---

## 17. CTE Questions

---

### Q53. What is a CTE? What are its advantages over subqueries?

**Answer:**
A CTE (Common Table Expression) is a named temporary result set defined with the `WITH` clause that exists only for the duration of the query.

```sql
WITH dept_avg AS (
    SELECT dept_id, AVG(salary) AS avg_sal
    FROM employees GROUP BY dept_id
)
SELECT e.name, e.salary, d.avg_sal
FROM employees e
JOIN dept_avg d ON e.dept_id = d.dept_id
WHERE e.salary > d.avg_sal;
```

**Advantages over subqueries:**
- **Readability:** Named and defined at the top — easier to understand
- **Reusability:** Can be referenced multiple times in the same query (subqueries cannot)
- **Recursive capability:** CTEs can be recursive; subqueries cannot
- **Debugging:** Easier to test each CTE independently

---

### Q54. What is a Recursive CTE? Give an example.

**Answer:**
A recursive CTE calls itself repeatedly to process hierarchical data (like an org chart, a tree, or folder structure).

```sql
WITH RECURSIVE org_chart AS (

    -- Anchor: Start with CEO (no manager)
    SELECT emp_id, name, manager_id, 1 AS level
    FROM employees
    WHERE manager_id IS NULL

    UNION ALL

    -- Recursive part: find direct reports of each found employee
    SELECT e.emp_id, e.name, e.manager_id, oc.level + 1
    FROM employees e
    INNER JOIN org_chart oc ON e.manager_id = oc.emp_id

)
SELECT level, name FROM org_chart ORDER BY level, name;
```

The recursion stops when no more rows satisfy the join condition (no more subordinates).

---

## 18. Normalization Questions

---

### Q55. What is Normalization? Why is it important?

**Answer:**
Normalization is the process of organizing a relational database to:
- Eliminate data redundancy (duplicate data)
- Avoid data anomalies (update, insert, delete anomalies)
- Ensure data integrity

**Anomaly examples (without normalization):**
- **Update anomaly:** Changing a department name requires updating 100 employee rows
- **Insert anomaly:** Cannot insert a department until at least one employee is assigned
- **Delete anomaly:** Deleting the last employee in a department also loses the department info

---

### Q56. Explain 1NF, 2NF, and 3NF with examples.

**Answer:**

**1NF (First Normal Form):**
Each cell must have atomic (single, indivisible) values. No repeating groups.

```
VIOLATES 1NF:
| emp_id | name  | skills            |
|--------|-------|-------------------|
| 1      | Alice | Java, SQL, Python  |

SATISFIES 1NF:
| emp_id | name  | skill  |
|--------|-------|--------|
| 1      | Alice | Java   |
| 1      | Alice | SQL    |
| 1      | Alice | Python |
```

**2NF (Second Normal Form):**
Must be 1NF + no partial dependency (every non-key column must depend on the entire primary key, not just part of it). Relevant when the primary key is composite.

```
VIOLATES 2NF (composite PK: order_id + product_id):
| order_id | product_id | product_name | quantity |
  product_name depends only on product_id, not the full PK → partial dependency

SATISFIES 2NF: Split into:
- orders_items(order_id, product_id, quantity)
- products(product_id, product_name)
```

**3NF (Third Normal Form):**
Must be 2NF + no transitive dependency (non-key columns must not depend on other non-key columns).

```
VIOLATES 3NF:
| emp_id | dept_id | dept_name |
  dept_name depends on dept_id (a non-key column), not directly on emp_id

SATISFIES 3NF: Split into:
- employees(emp_id, dept_id)
- departments(dept_id, dept_name)
```

---

### Q57. What is Denormalization? When would you use it?

**Answer:**
Denormalization is the intentional introduction of redundancy into a database to improve read performance, at the cost of increased storage and more complex writes.

**When to use:**
- Data warehousing and reporting (OLAP) — queries are complex but data rarely changes
- High-traffic read-heavy applications where JOIN overhead is too costly
- Caching frequently joined data together

```sql
-- Normalized (needs JOIN for every query):
employees(emp_id, name, dept_id) + departments(dept_id, dept_name)

-- Denormalized (dept_name stored directly in employees):
employees(emp_id, name, dept_id, dept_name)
-- Faster reads, but dept_name must be updated in many rows if it changes
```

---

## 19. Query Optimization Questions

---

### Q58. How would you optimize a slow SQL query?

**Answer:**
Step-by-step approach:

1. **Use EXPLAIN / EXPLAIN ANALYZE** to see the execution plan and identify bottlenecks (full table scans, missing indexes)
2. **Check indexes** — ensure columns in WHERE, JOIN ON, ORDER BY, GROUP BY are indexed
3. **Avoid SELECT \*** — fetch only the columns you need
4. **Avoid functions on indexed columns** in WHERE clause
5. **Replace OR with UNION ALL** if it prevents index usage
6. **Use EXISTS instead of IN** for large subqueries
7. **Limit result sets** — use LIMIT / pagination
8. **Avoid correlated subqueries** — replace with JOINs or window functions
9. **Use covering indexes** where possible
10. **Partition large tables** — reduce data scanned

---

### Q59. Why does using a function in WHERE prevent index usage?

**Answer:**
When you wrap a column in a function, the database cannot use an index on that column because the index stores raw column values, not the function's output.

```sql
-- BAD: Index on hire_date cannot be used
SELECT * FROM employees WHERE YEAR(hire_date) = 2022;

-- GOOD: Rewritten to allow index on hire_date
SELECT * FROM employees WHERE hire_date BETWEEN '2022-01-01' AND '2022-12-31';

-- BAD: Index on name cannot be used
SELECT * FROM employees WHERE UPPER(name) = 'ALICE';

-- GOOD: Use a functional index (if supported) or store data consistently
SELECT * FROM employees WHERE name = 'Alice';
```

---

### Q60. What is a covering index?

**Answer:**
A covering index includes all the columns that a specific query needs — so the database can answer the query entirely from the index without reading the actual table rows (avoids a "key lookup" or "bookmark lookup").

```sql
-- Query needs dept_id and salary only
SELECT dept_id, salary FROM employees WHERE dept_id = 3 ORDER BY salary;

-- Covering index: includes all referenced columns
CREATE INDEX idx_covering ON employees(dept_id, salary);
-- Database reads only the index — no access to the main table needed
```

Covering indexes significantly improve read performance for frequently run reports.

---

## 20. Advanced & Tricky Questions

---

### Q61. How would you find duplicate records in a table?

**Answer:**
```sql
-- Find duplicate emails
SELECT email, COUNT(*) AS occurrences
FROM employees
GROUP BY email
HAVING COUNT(*) > 1;

-- Show all duplicate rows
SELECT *
FROM employees
WHERE email IN (
    SELECT email FROM employees
    GROUP BY email HAVING COUNT(*) > 1
)
ORDER BY email;
```

---

### Q62. How would you delete duplicate rows but keep one?

**Answer:**
```sql
-- Keep the row with the lowest emp_id for each email (MySQL)
DELETE FROM employees
WHERE emp_id NOT IN (
    SELECT MIN(emp_id)
    FROM employees
    GROUP BY email
);

-- PostgreSQL: Using CTE
WITH ranked AS (
    SELECT emp_id,
           ROW_NUMBER() OVER (PARTITION BY email ORDER BY emp_id) AS rn
    FROM employees
)
DELETE FROM employees WHERE emp_id IN (SELECT emp_id FROM ranked WHERE rn > 1);
```

---

### Q63. How would you find the second highest salary?

**Answer:**
```sql
-- Method 1: Subquery (simple)
SELECT MAX(salary) AS second_highest
FROM employees
WHERE salary < (SELECT MAX(salary) FROM employees);

-- Method 2: DENSE_RANK (handles ties correctly)
SELECT salary FROM (
    SELECT salary,
           DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk
    FROM employees
) ranked
WHERE rnk = 2
LIMIT 1;

-- Method 3: LIMIT/OFFSET (MySQL)
SELECT DISTINCT salary FROM employees
ORDER BY salary DESC
LIMIT 1 OFFSET 1;
```

---

### Q64. What does NOT IN return when the subquery contains a NULL?

**Answer:**
This is a classic SQL trap. When the subquery contains even one NULL value, `NOT IN` returns **no rows at all**.

```sql
-- dept_id values: 1, 2, NULL
SELECT * FROM employees WHERE dept_id NOT IN (1, 2, NULL);
-- Returns EMPTY RESULT SET!

-- Explanation:
-- NOT IN (1, 2, NULL) is equivalent to:
-- dept_id != 1 AND dept_id != 2 AND dept_id != NULL
-- dept_id != NULL is UNKNOWN for every row → entire condition is UNKNOWN

-- SAFE alternative: Use NOT EXISTS
SELECT * FROM employees e
WHERE NOT EXISTS (
    SELECT 1 FROM departments d WHERE d.dept_id = e.dept_id
);
```

---

### Q65. What is the difference between a HAVING clause filter and a WHERE clause filter on performance?

**Answer:**
- `WHERE` filters rows **before** aggregation — less data is processed during grouping → faster
- `HAVING` filters groups **after** aggregation — all rows are grouped first, then filtered → potentially slower

**Best practice:** Always push filters to WHERE when possible, and only use HAVING for conditions that involve aggregate results.

```sql
-- LESS EFFICIENT: HAVING filters after full scan + group
SELECT dept_id, COUNT(*)
FROM employees
HAVING dept_id = 3;  -- Should be WHERE!

-- MORE EFFICIENT: WHERE filters before grouping
SELECT dept_id, COUNT(*)
FROM employees
WHERE dept_id = 3
GROUP BY dept_id;
```

---

### Q66. What is the difference between OLTP and OLAP?

**Answer:**

| Feature | OLTP | OLAP |
|---------|------|------|
| Full Form | Online Transaction Processing | Online Analytical Processing |
| Purpose | Day-to-day operations | Reporting, analysis |
| Data volume | Small transactions | Large volume reads |
| Query type | Simple INSERT/UPDATE/DELETE | Complex SELECT with aggregations |
| Normalization | Highly normalized | Often denormalized (star/snowflake schema) |
| Response time | Milliseconds | Seconds to minutes |
| Examples | Banking, e-commerce, CRM | Data warehouses, BI dashboards |

---

### Q67. What is a Star Schema vs Snowflake Schema?

**Answer:**

**Star Schema:**
- Central fact table surrounded by denormalized dimension tables
- Simple, fewer JOINs, faster queries
- More redundant data

```
              DIM_Customer
                   |
DIM_Date — FACT_Sales — DIM_Product
                   |
              DIM_Region
```

**Snowflake Schema:**
- Dimension tables are further normalized into sub-dimensions
- Less redundancy, more JOINs needed, slower queries
- Saves storage

Used in data warehouses for reporting and analytics.

---

### Q68. How would you calculate a running total?

**Answer:**
```sql
SELECT
    order_date,
    amount,
    SUM(amount) OVER (
        ORDER BY order_date
        ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
    ) AS running_total
FROM orders;
```

`ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW` means: sum all rows from the very first row up to the current row.

---

### Q69. What is the difference between ROWS and RANGE in window frames?

**Answer:**

```sql
-- ROWS: Physical rows — counts actual rows
SUM(salary) OVER (ORDER BY salary ROWS BETWEEN 1 PRECEDING AND CURRENT ROW)
-- Sums the current row and the single row physically before it

-- RANGE: Logical range — includes all rows with the same ORDER BY value as peers
SUM(salary) OVER (ORDER BY salary RANGE BETWEEN 1 PRECEDING AND CURRENT ROW)
-- Includes all rows with salary values within 1 unit of the current row's salary
```

When there are duplicate values in the ORDER BY column, `RANGE` includes all duplicates as peers, while `ROWS` counts them as separate physical rows.

---

### Q70. How would you pivot rows into columns without using PIVOT keyword?

**Answer:**
Use `CASE` expressions inside aggregate functions.

```sql
-- Original data: dept_id, gender, count
-- Goal: Show male and female headcount side by side per dept

SELECT
    dept_id,
    SUM(CASE WHEN gender = 'M' THEN 1 ELSE 0 END) AS male_count,
    SUM(CASE WHEN gender = 'F' THEN 1 ELSE 0 END) AS female_count,
    COUNT(*) AS total
FROM employees
GROUP BY dept_id
ORDER BY dept_id;
```

This technique works across all SQL databases and doesn't require the `PIVOT` keyword (which is only in SQL Server / Oracle).

---

## Quick Revision Summary

### Most Common Interview Questions
- Difference between DELETE / TRUNCATE / DROP
- INNER JOIN vs LEFT JOIN
- WHERE vs HAVING
- Primary Key vs Unique Key
- Correlated subquery vs regular subquery
- RANK vs DENSE_RANK vs ROW_NUMBER
- What is a CTE and why use it
- ACID properties explained
- How indexes work
- How to find second highest salary
- NOT IN with NULL trap
- Normalization: 1NF, 2NF, 3NF

### Key SQL Rules to Remember
- NULL compared with anything using `=` is always NULL (not TRUE)
- `NOT IN` fails silently when subquery has NULLs — use NOT EXISTS
- WHERE cannot use aggregate functions — use HAVING
- SELECT alias cannot be used in WHERE (execution order)
- TRUNCATE cannot be rolled back in most databases
- A table can have only ONE clustered index
- Foreign key column must match the data type of the referenced primary key

---

*Practice these questions with real queries on sample data (like the classic `employees`, `departments`, `orders` tables) to build solid SQL interview skills.*
