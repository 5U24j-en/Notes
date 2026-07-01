# 🗄️ SQL Functions & Code Templates Cheat Sheet — Data Engineer Interviews (GCCs)

---
## 1. Aggregate Functions


```sql
-- --- Core Aggregates ---
COUNT(*) -- count all rows (incl NULLs)
COUNT(column) -- count non-NULL values
COUNT(DISTINCT column) -- count unique non-NULL values
SUM(amount) -- total
AVG(salary) -- average (ignores NULLs)
MIN(price) MAX(price) -- min / max 

-- --- With GROUP BY ---
SELECT department, COUNT(*) AS headcount, AVG(salary) AS avg_sal
FROM employees
GROUP BY department
HAVING COUNT(*) > 5; -- filter AFTER grouping

-- --- Conditional Aggregation (VERY common in interviews) ---
COUNT(CASE WHEN status = 'active' THEN 1 END) AS active_count
SUM(CASE WHEN year = 2024 THEN revenue ELSE 0 END) AS rev_2024
AVG(CASE WHEN dept = 'Eng' THEN salary END) AS eng_avg
COUNT(DISTINCT CASE WHEN event = 'purchase' THEN user_id END) AS buyers
```

---
## 2. Window / Analytic Functions ⭐

```sql
-- --- Ranking ---
ROW_NUMBER() OVER (PARTITION BY dept ORDER BY salary DESC) -- always unique: 1,2,3,4
RANK() OVER (PARTITION BY dept ORDER BY salary DESC) -- ties share, skip: 1,2,2,4
DENSE_RANK() OVER (PARTITION BY dept ORDER BY salary DESC) -- ties share, no skip: 1,2,2,3
NTILE(4) OVER (ORDER BY salary) -- split into 4 buckets (quartiles)
PERCENT_RANK() OVER (ORDER BY salary) -- percentile rank 0-1 

-- --- Offset / Navigation ---
LAG(col, 1, default) OVER (ORDER BY dt) -- previous row value
LEAD(col, 1, default) OVER (ORDER BY dt) -- next row value
FIRST_VALUE(col) OVER (PARTITION BY grp ORDER BY dt) -- first in window
LAST_VALUE(col) OVER (PARTITION BY grp ORDER BY dt
ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING) -- last in window
NTH_VALUE(col, 2) OVER (ORDER BY dt) -- 2nd value in window

-- --- Running / Cumulative Aggregates ---
SUM(amount) OVER (ORDER BY dt ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS running_total
AVG(amount) OVER (ORDER BY dt ROWS BETWEEN 6 PRECEDING AND CURRENT ROW) AS moving_avg_7d
COUNT(*) OVER (ORDER BY dt ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS cumulative_cnt
MAX(price) OVER (ORDER BY dt ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS running_max


-- --- Frame Clause Quick Reference ---
-- ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW → running total (default)
-- ROWS BETWEEN 6 PRECEDING AND CURRENT ROW → 7-row sliding window
-- ROWS BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING → reverse running
-- ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING → entire partition
-- ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING → 3-row centered window
```

---
## 3. String Functions

```sql
-- --- Case ---
UPPER('hello') -- 'HELLO'
LOWER('HELLO') -- 'hello'
INITCAP('hello world') -- 'Hello World' (Postgres/Oracle)

-- --- Trim / Pad ---
TRIM(' hi ') -- 'hi'
LTRIM(' hi') RTRIM('hi ')
LPAD('42', 5, '0') -- '00042'
RPAD('hi', 5, '.') -- 'hi...'

-- --- Extract / Slice ---
SUBSTRING('Hello World', 1, 5) -- 'Hello' (ANSI: FROM 1 FOR 5)
LEFT('Hello', 3) -- 'Hel'
RIGHT('Hello', 3) -- 'llo'
LENGTH('Hello') -- 5 (LEN in SQL Server)
CHAR_LENGTH('Hello') -- 5

-- --- Search / Find ---
POSITION('World' IN 'Hello World') -- 7 (CHARINDEX in SQL Server)
INSTR('Hello World', 'World') -- 7 (Oracle/MySQL)

-- --- Replace / Modify ---
REPLACE('2024-01-01', '-', '/') -- '2024/01/01'
TRANSLATE('abc', 'abc', 'xyz') -- 'xyz' (char-by-char replace)
REVERSE('hello') -- 'olleh'
REPEAT('ab', 3) -- 'ababab'

-- --- Split / Concat ---
CONCAT(first_name, ' ', last_name)
first_name || ' ' || last_name -- pipe concat (Postgres/Oracle)
CONCAT_WS(',', col1, col2, col3) -- concat with separator
STRING_AGG(name, ', ' ORDER BY name) -- Postgres: group concat
GROUP_CONCAT(name ORDER BY name SEPARATOR ', ') -- MySQL
LISTAGG(name, ', ') WITHIN GROUP (ORDER BY name) -- Oracle
SPLIT_PART('a,b,c', ',', 2) -- 'b' (Postgres)

-- --- Regex ---
col LIKE '%pattern%' -- basic wildcard
col SIMILAR TO '%(abc|def)%' -- SQL standard regex
REGEXP_LIKE(col, '^[A-Z]+$') -- Oracle/MySQL 8
col ~ '^[A-Z]+$' -- Postgres regex match
REGEXP_REPLACE(col, '[^0-9]', '', 'g') -- extract digits only
REGEXP_SUBSTR(col, '[0-9]+') -- extract first number match
```

---
## 4. Date / Time Functions

```sql
-- --- Current Date/Time ---
CURRENT_DATE CURRENT_TIMESTAMP NOW() -- (MySQL/Postgres)
GETDATE() SYSDATETIME() -- (SQL Server)
SYSDATE -- (Oracle)

-- --- Extract Parts ---
EXTRACT(YEAR FROM order_date) -- ANSI standard
YEAR(order_date) -- MySQL / SQL Server
DATE_PART('month', order_date) -- Postgres
TO_CHAR(order_date, 'YYYY-MM') -- Oracle/Postgres formatting

-- --- Date Arithmetic ---
order_date + INTERVAL '7' DAY -- add 7 days (ANSI)
DATE_ADD(order_date, INTERVAL 7 DAY) -- MySQL
DATEADD(DAY, 7, order_date) -- SQL Server
order_date + 7 -- Oracle (days)

-- --- Date Difference ---
DATEDIFF(DAY, start_date, end_date) -- SQL Server
DATE_DIFF(end_date, start_date, DAY) -- BigQuery
end_date - start_date -- Postgres (returns interval)
TIMESTAMPDIFF(MONTH, start_date, end_date) -- MySQL

-- --- Truncate ---
DATE_TRUNC('month', order_date) -- Postgres/BigQuery: 2024-03-15 → 2024-03-01
TRUNC(order_date, 'MM') -- Oracle
DATEFROMPARTS(2024, 1, 1) -- SQL Server

-- --- Parse / Convert ---
TO_DATE('2024-01-15', 'YYYY-MM-DD') -- Oracle/Postgres
STR_TO_DATE('15/01/2024', '%d/%m/%Y') -- MySQL
CAST('2024-01-15' AS DATE) -- ANSI
FORMAT(order_date, 'yyyy-MM-dd') -- SQL Server
```
---
## 5. NULL Handling

```sql
-- --- COALESCE (first non-NULL) ---
COALESCE(phone, mobile, 'N/A') -- returns first non-NULL value
COALESCE(col, 0) -- replace NULL with 0

-- --- IFNULL / ISNULL / NVL ---
IFNULL(col, 0) -- MySQL
ISNULL(col, 0) -- SQL Server
NVL(col, 0) -- Oracle
NVL2(col, 'has value', 'is null') -- Oracle: if not null / if null

-- --- NULLIF ---
NULLIF(a, b) -- returns NULL if a = b, else a

-- Use case: avoid division by zero
revenue / NULLIF(cost, 0) -- returns NULL instead of error

-- --- NULL checks ---
col IS NULL col IS NOT NULL
-- ⚠️ NEVER use col = NULL (always false!)

```

---
## 6. CASE / Conditional Logic

  

```sql

-- --- Simple CASE ---
CASE status
WHEN 'A' THEN 'Active'
WHEN 'I' THEN 'Inactive'
ELSE 'Unknown'
END AS status_label

-- --- Searched CASE (more flexible) ---
CASE
WHEN salary > 100000 THEN 'Senior'
WHEN salary > 60000 THEN 'Mid'
ELSE 'Junior'
END AS band

-- --- CASE in aggregation (PIVOT pattern) ---
SUM(CASE WHEN month = 'Jan' THEN revenue ELSE 0 END) AS jan_rev  

-- --- CASE in ORDER BY ---
ORDER BY CASE WHEN status = 'urgent' THEN 0 ELSE 1 END, created_at

-- --- IIF (SQL Server / Access shorthand) ---
IIF(salary > 100000, 'High', 'Low')

-- --- DECODE (Oracle shorthand for simple CASE) ---
DECODE(status, 'A', 'Active', 'I', 'Inactive', 'Unknown')
```

---
## 7. JOINs — Complete Reference

```sql
-- --- INNER JOIN (only matches) ---
SELECT * FROM orders o INNER JOIN customers c ON o.cust_id = c.id;  

-- --- LEFT JOIN (all left + matching right) ---
SELECT * FROM orders o LEFT JOIN customers c ON o.cust_id = c.id;

-- --- RIGHT JOIN ---
SELECT * FROM orders o RIGHT JOIN customers c ON o.cust_id = c.id;

-- --- FULL OUTER JOIN ---
SELECT * FROM orders o FULL OUTER JOIN customers c ON o.cust_id = c.id;

-- --- CROSS JOIN (cartesian product) ---
SELECT * FROM dates d CROSS JOIN products p; -- every combination

-- --- SELF JOIN (employee-manager) ---
SELECT e.name AS emp, m.name AS mgr
FROM employees e LEFT JOIN employees m ON e.manager_id = m.id;

-- --- Anti-Join patterns (find unmatched) ---

-- Method 1: LEFT JOIN + IS NULL
SELECT o.* FROM orders o
LEFT JOIN customers c ON o.cust_id = c.id
WHERE c.id IS NULL;


-- Method 2: NOT EXISTS
SELECT * FROM orders o
WHERE NOT EXISTS (SELECT 1 FROM customers c WHERE c.id = o.cust_id);

-- Method 3: NOT IN (⚠️ beware NULLs)
SELECT * FROM orders WHERE cust_id NOT IN (SELECT id FROM customers WHERE id IS NOT NULL);

-- --- Semi-Join (exists check, no extra columns) ---
SELECT * FROM orders o WHERE EXISTS (SELECT 1 FROM vip_customers v WHERE v.id = o.cust_id);
```

  

---

  

## 8. Set Operations

  

```sql
-- --- UNION / UNION ALL ---
SELECT name FROM employees UNION ALL SELECT name FROM contractors; -- keeps dupes (faster)
SELECT name FROM employees UNION SELECT name FROM contractors; -- removes dupes

-- --- INTERSECT (common rows) ---
SELECT cust_id FROM orders_2023 INTERSECT SELECT cust_id FROM orders_2024;

-- --- EXCEPT / MINUS (in left but not right) ---
SELECT cust_id FROM orders_2023 EXCEPT SELECT cust_id FROM orders_2024; -- churned customers
-- Oracle uses MINUS instead of EXCEPT
```

  

---

  

## 9. CTE & Subquery Patterns

  

```sql
-- --- Standard CTE ---
WITH monthly_rev AS (
SELECT DATE_TRUNC('month', order_date) AS month, SUM(amount) AS revenue
FROM orders GROUP BY 1
)
SELECT * FROM monthly_rev WHERE revenue > 100000;


-- --- Multiple CTEs ---
WITH
cte1 AS (SELECT ...),
cte2 AS (SELECT ... FROM cte1) -- can reference previous CTEs
SELECT * FROM cte2;

-- --- Recursive CTE (hierarchy traversal) ---
WITH RECURSIVE org AS (
SELECT id, name, manager_id, 1 AS depth FROM employees WHERE manager_id IS NULL -- anchor
UNION ALL
SELECT e.id, e.name, e.manager_id, o.depth + 1
FROM employees e JOIN org o ON e.manager_id = o.id -- recurse
)
SELECT * FROM org;

-- --- Scalar Subquery ---
SELECT name, salary,
(SELECT AVG(salary) FROM employees) AS company_avg
FROM employees;

-- --- Correlated Subquery ---
SELECT * FROM employees e1
WHERE salary > (SELECT AVG(salary) FROM employees e2 WHERE e2.dept = e1.dept);

-- --- Derived Table (Inline View) ---
SELECT dept, avg_sal FROM (
SELECT dept, AVG(salary) AS avg_sal FROM employees GROUP BY dept
) t WHERE avg_sal > 80000;

```

  

---

  

## 10. MERGE / UPSERT (SCD Type 1)

  

```sql

-- --- ANSI MERGE ---
MERGE INTO target t
USING source s ON t.id = s.id
WHEN MATCHED THEN
UPDATE SET t.name = s.name, t.updated_at = CURRENT_TIMESTAMP
WHEN NOT MATCHED THEN
INSERT (id, name, created_at) VALUES (s.id, s.name, CURRENT_TIMESTAMP);


-- --- MySQL UPSERT ---
INSERT INTO target (id, name) VALUES (1, 'Alice')
ON DUPLICATE KEY UPDATE name = VALUES(name);

  
-- --- Postgres UPSERT ---
INSERT INTO target (id, name) VALUES (1, 'Alice')
ON CONFLICT (id) DO UPDATE SET name = EXCLUDED.name;
```

  

---

  

## 11. Type Conversion / CAST

  

```sql
CAST(col AS INTEGER) -- ANSI standard
CAST(col AS VARCHAR(100))
CAST(col AS DATE)
CAST(col AS DECIMAL(10,2))

col::INTEGER -- Postgres shorthand
col::TEXT
col::DATE

  
CONVERT(VARCHAR, order_date, 103) -- SQL Server (format 103 = dd/mm/yyyy)
TRY_CAST(col AS INT) -- SQL Server: returns NULL on failure (safe)
TRY_CONVERT(INT, col) -- SQL Server: same idea
SAFE_CAST(col AS INT64) -- BigQuery: returns NULL on failure
TO_NUMBER('123.45', '999.99') -- Oracle
```

  

---

  

## 12. Useful Utility Functions

  

```sql

-- --- GREATEST / LEAST ---

GREATEST(col1, col2, col3) -- max across columns (not rows)

LEAST(col1, col2, col3) -- min across columns

  

-- --- GENERATE_SERIES (fill gaps in data) ---

SELECT generate_series('2024-01-01'::date, '2024-12-31'::date, '1 day') -- Postgres

-- BigQuery: GENERATE_DATE_ARRAY('2024-01-01', '2024-12-31', INTERVAL 1 DAY)

  

-- --- ROW / ARRAY constructors ---

col IN (1, 2, 3)

col = ANY(ARRAY[1, 2, 3]) -- Postgres array syntax

  

-- --- EXISTS (boolean check) ---

WHERE EXISTS (SELECT 1 FROM ... WHERE ...)

  

-- --- DISTINCT ON (Postgres — first row per group) ---

SELECT DISTINCT ON (dept) * FROM employees ORDER BY dept, salary DESC;

-- Returns highest-paid employee per department — elegant alternative to ROW_NUMBER

  

-- --- QUALIFY (BigQuery / Snowflake — filter window results inline) ---

SELECT * FROM employees

QUALIFY ROW_NUMBER() OVER (PARTITION BY dept ORDER BY salary DESC) = 1;

-- Replaces the CTE + WHERE rn = 1 pattern

```

  

---

  

## 13. Interview Code Templates ⭐

### Template 1: Nth Highest per Group

```sql
WITH ranked AS (
SELECT *, DENSE_RANK() OVER (PARTITION BY department ORDER BY salary DESC) AS dr
FROM employees
)
SELECT * FROM ranked WHERE dr = N; -- replace N with desired rank
```

### Template 2: Consecutive Days / Streaks (Islands)

```sql
WITH grouped AS (
SELECT user_id, login_date,
login_date - CAST(ROW_NUMBER() OVER (PARTITION BY user_id ORDER BY login_date) AS INT) * INTERVAL '1 day' AS grp
FROM (SELECT DISTINCT user_id, login_date FROM logins) t
)
SELECT user_id, MIN(login_date) AS start_dt, MAX(login_date) AS end_dt, COUNT(*) AS streak
FROM grouped GROUP BY user_id, grp
HAVING COUNT(*) >= 3;
```

### Template 3: Gaps in Sequence

```sql
SELECT prev_id + 1 AS gap_start, next_id - 1 AS gap_end
FROM (
SELECT id AS prev_id, LEAD(id) OVER (ORDER BY id) AS next_id FROM sequence_table
) t WHERE next_id - prev_id > 1;
```


### Template 4: Year-over-Year Growth

```sql
WITH yearly AS (
SELECT EXTRACT(YEAR FROM dt) AS yr, SUM(revenue) AS rev FROM sales GROUP BY 1
)
SELECT yr, rev, LAG(rev) OVER (ORDER BY yr) AS prev_rev,
ROUND((rev - LAG(rev) OVER (ORDER BY yr)) * 100.0 / LAG(rev) OVER (ORDER BY yr), 2) AS yoy_pct
FROM yearly;
```


### Template 5: Running Total

```sql
SELECT dt, amount,
SUM(amount) OVER (ORDER BY dt ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS running_total
FROM transactions;
```

### Template 6: Moving Average (7-Day)

```sql
SELECT dt, value,
AVG(value) OVER (ORDER BY dt ROWS BETWEEN 6 PRECEDING AND CURRENT ROW) AS ma_7d
FROM metrics;
```

### Template 7: PIVOT with CASE

```sql
SELECT region,
SUM(CASE WHEN quarter = 'Q1' THEN revenue END) AS Q1,
SUM(CASE WHEN quarter = 'Q2' THEN revenue END) AS Q2,
SUM(CASE WHEN quarter = 'Q3' THEN revenue END) AS Q3,
SUM(CASE WHEN quarter = 'Q4' THEN revenue END) AS Q4
FROM sales GROUP BY region;
```

### Template 8: De-duplication

```sql
-- Keep only the latest record per key
WITH deduped AS (
SELECT *, ROW_NUMBER() OVER (PARTITION BY email ORDER BY updated_at DESC) AS rn
FROM users
)
SELECT * FROM deduped WHERE rn = 1;
```

### Template 9: Funnel / Conversion Analysis

```sql
SELECT
COUNT(DISTINCT CASE WHEN step = 'view' THEN user_id END) AS views,
COUNT(DISTINCT CASE WHEN step = 'cart' THEN user_id END) AS carts,
COUNT(DISTINCT CASE WHEN step = 'purchase' THEN user_id END) AS purchases,
ROUND(COUNT(DISTINCT CASE WHEN step = 'purchase' THEN user_id END) * 100.0
/ NULLIF(COUNT(DISTINCT CASE WHEN step = 'view' THEN user_id END), 0), 2) AS conversion_pct
FROM events;
```

### Template 10: Median (without PERCENTILE)

```sql
WITH ordered AS (
SELECT salary, ROW_NUMBER() OVER (ORDER BY salary) AS rn, COUNT(*) OVER() AS n
FROM employees
)
SELECT AVG(salary) AS median FROM ordered
WHERE rn IN (FLOOR((n+1)/2.0), CEIL((n+1)/2.0));
```

### Template 11: Self-Join — Employees Earning More Than Manager

```sql
SELECT e.name AS employee, e.salary, m.name AS manager, m.salary AS mgr_salary
FROM employees e JOIN employees m ON e.manager_id = m.id
WHERE e.salary > m.salary;
```

### Template 12: Cumulative Distribution / Percentile

```sql
SELECT name, salary,
CUME_DIST() OVER (ORDER BY salary) AS cum_dist, -- % of rows ≤ current
PERCENT_RANK() OVER (ORDER BY salary) AS pct_rank, -- relative rank as %
NTILE(4) OVER (ORDER BY salary) AS quartile -- bucket 1-4
FROM employees;
```

### Template 13: Date Spine (Fill Missing Dates)

```sql
-- Postgres example
WITH date_spine AS (
SELECT generate_series(MIN(order_date), MAX(order_date), '1 day'::interval)::date AS dt
FROM orders
),
daily AS (
SELECT order_date, SUM(amount) AS revenue FROM orders GROUP BY 1
)
SELECT d.dt, COALESCE(dl.revenue, 0) AS revenue
FROM date_spine d LEFT JOIN daily dl ON d.dt = dl.order_date
ORDER BY d.dt;
```


### Template 14: SCD Type 2 (Slowly Changing Dimension)

```sql
-- Close old record + insert new record
UPDATE dim_customer SET is_current = FALSE, end_date = CURRENT_DATE
WHERE customer_id IN (SELECT customer_id FROM staging WHERE ...) AND is_current = TRUE;
INSERT INTO dim_customer (customer_id, name, email, start_date, end_date, is_current)
SELECT customer_id, name, email, CURRENT_DATE, '9999-12-31', TRUE FROM staging;
```

### Template 15: Session Detection (Sessionization)

```sql
WITH flagged AS (
SELECT user_id, event_time,
CASE WHEN event_time - LAG(event_time) OVER (PARTITION BY user_id ORDER BY event_time)
> INTERVAL '30 minutes' THEN 1 ELSE 0 END AS new_session
FROM events
),
sessions AS (
SELECT *, SUM(new_session) OVER (PARTITION BY user_id ORDER BY event_time) AS session_id
FROM flagged
)
SELECT user_id, session_id, MIN(event_time) AS start, MAX(event_time) AS end,
COUNT(*) AS events
FROM sessions GROUP BY user_id, session_id;
```


---


## 14. Query Execution Order


```

Written: SELECT → FROM → WHERE → GROUP BY → HAVING → ORDER BY → LIMIT

Executed: FROM → JOIN → WHERE → GROUP BY → HAVING → SELECT → DISTINCT → ORDER BY → LIMIT

```

  

> ⚠️ This is why you **can't** use a column alias in WHERE but **can** in ORDER BY.

  

---

  

## 15. Quick Reference Table

  

| Task | Function / Pattern |

|---|---|

| Count unique values | `COUNT(DISTINCT col)` |

| Replace NULLs | `COALESCE(col, default)` |

| Safe division | `x / NULLIF(y, 0)` |

| Rank without gaps | `DENSE_RANK() OVER(...)` |

| Previous row value | `LAG(col, 1) OVER(ORDER BY ...)` |

| Running total | `SUM(col) OVER(ORDER BY dt ROWS UNBOUNDED PRECEDING)` |

| 7-day moving avg | `AVG(col) OVER(ORDER BY dt ROWS 6 PRECEDING)` |

| Top N per group | `ROW_NUMBER() OVER(PARTITION BY grp ORDER BY col DESC)` + `WHERE rn <= N` |

| De-duplicate rows | `ROW_NUMBER() OVER(PARTITION BY key ORDER BY ts DESC)` + `WHERE rn = 1` |

| Consecutive streaks | `date - ROW_NUMBER()` grouping trick |

| Find gaps | `LEAD(id) OVER(ORDER BY id) - id > 1` |

| Pivot rows→cols | `SUM(CASE WHEN ... THEN col END)` |

| Concat group values | `STRING_AGG(col, ',')` / `GROUP_CONCAT` / `LISTAGG` |

| UPSERT | `MERGE ... WHEN MATCHED ... WHEN NOT MATCHED` |

| Anti-join | `LEFT JOIN ... WHERE right.id IS NULL` |

| Hierarchy walk | `WITH RECURSIVE ...` |

| Fill missing dates | Date spine `LEFT JOIN` actual data |

| Sessionize events | `LAG` + threshold flag + cumulative `SUM` |

| YoY growth | `LAG(metric) OVER(ORDER BY year)` + % calc |

| Percentile bucket | `NTILE(4) OVER(ORDER BY col)` |

  

---

  

> **💡 Interview Tips:**

> - Always mention query execution order when explaining window functions.

> - Use `UNION ALL` over `UNION` in pipelines — say **why** (performance).

> - Prefer `EXISTS` over `IN` for correlated checks — explain NULL safety.

> - For "find Nth" questions, clarify: ROW_NUMBER (arbitrary tiebreak) vs DENSE_RANK (handles ties).

> - When writing MERGE, mention **idempotency** — interviewers love that word.