
## SQL Commands Execution Order


- Understanding the order in which a SQL statement executes is one of the most important concepts for writing accurate and efficient queries. 
- There is a big difference between the **lexical order** (how you type the query) and the **logical execution order** (the sequence in which the database engine processes the data). 
- Here is the exact step-by-step logical execution order of a standard SQL query:

	- #### 1. `FROM` (and `JOIN`)
		The database first needs to know where the data is coming from. It grabs the working tables and joins them together to create a massive, combined base dataset.
	- #### 2. `WHERE`
		Once the base dataset is assembled, the engine filters out individual rows that do not meet the conditions specified in the `WHERE` clause. *(Note: You cannot use aliases created in the `SELECT` clause here, because `SELECT` hasn't happened yet!)*
	- #### 3. `GROUP BY`
		The remaining rows are then grouped together based on common values in the columns specified in the `GROUP BY` clause. 
	- #### 4. `HAVING`
		If you grouped your data, the `HAVING` clause filters those *groups*. It works exactly like `WHERE`, but it applies to the aggregated groups rather than individual rows.
	- #### 5. `SELECT`
		Finally, the engine looks at the `SELECT` clause to determine which specific columns, math, or aggregations (like `SUM` or `COUNT`) to return. This is also the step where column aliases (using `AS`) are created.
	- #### 6. `DISTINCT`
		If you used the `DISTINCT` keyword, the database now scans the selected columns and removes any duplicate rows.
	- #### 7. `ORDER BY`
		Now that the final dataset has been selected, the database sorts the results according to the `ORDER BY` clause. Because `SELECT` has already happened, you *can* use your column aliases here.
	- #### 8. `LIMIT` / `OFFSET`
		Finally, if you requested only a specific number of rows, the database applies the `LIMIT` (and skips rows if `OFFSET` is used) to give you the exact slice of data you asked for.

 - ##### Written vs. Executed Comparison
	- To see how drastically different your writing is from the engine's thinking, compare them side-by-side:

	`How you write it:`
	
```sql
1. SELECT column_name, COUNT(*) AS total
2. FROM table_name
3. WHERE condition = true
4. GROUP BY column_name
5. HAVING COUNT(*) > 5
6. ORDER BY total DESC
7. LIMIT 10;
```

	How the database executes it:

```sql
1. FROM table_name
2. WHERE condition = true
3. GROUP BY column_name
4. HAVING COUNT(*) > 5
5. SELECT column_name, COUNT(*) AS total
6. ORDER BY total DESC
7. LIMIT 10;
```

> [!note]
> While this is the strict logical order, modern database engines contain highly advanced Query Optimizers. The optimizer might physically execute the steps in a slightly different order behind the scenes to save time and memory, but it guarantees that the final output will always strictly match the logic of the order listed above.

### ERROR - Example 1:

```sql
SELECT emp_id, name, dept, city, salary
FROM Employees
ROW_NUMBER() OVER (PARTITION BY dept ORDER BY salary) AS row_number
WHERE row_number <= 2;
```

The above **will NOT WORK** because , `WHERE` is executes before the `ROW_NUMBER() / PARTITION` function, If `WHERE` is executed before creating column row_number, how can this work as row_number does not exist.

Correct Way is to use sub queries , first create a sub table and then select and filter from that.

```sql
SELECT emp_id, name, dept, city, salary
FROM 
(
	SELECT  
		emp_id, 
		name,
		dept, 
		city, 
		salary , 
		ROW_NUMBER() OVER (PARTITION BY dept ORDER BY salary) AS row_number
	FROM Employees
) e
WHERE row_number <= 2;
```

### ERROR Example 2 

```sql
SELECT city , COUNT(name) AS no_of_people
FROM employees
GROUP BY city
HAVING no_of_people = 1;
```

- The Above code will throw **error** , as `SELECT` is after the `HAVING` in the execution order , hence the column **no_of_people is not created** 

- Correct Code
```sql
SELECT city , COUNT(name) AS no_of_people
FROM employees
GROUP BY city
HAVING COUNT(name) = 1;
```

