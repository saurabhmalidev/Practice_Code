###Q1. Find Customers Who Never Placed an Order 
Q2. Find the Second Highest Salary
Q3. Top Selling Products
Q4. Find Duplicate Email Addresses
Q5. Month-over-Month Sales Growth
Q6. Running Total of Customer Orders
Q7. Top 3 Highest Paid Employees in Each Department
Q8. Customers with Consecutive Purchase Days.
Q9. First and Last Order for Each Customer
Q10. Highest Paid Employee(s) in Each Department


======================================
======================================
**Q1. Find Customers Who Never Placed an Order**
| Column      | Data Type | Description          |
| ----------- | --------- | -------------------- |
| customer_id | INT       | Primary Key          |
| name        | VARCHAR   | Customer Name        |
| email       | VARCHAR   | Customer Email       |
| signup_date | DATE      | Date of registration |

| Column      | Data Type     | Description                         |
| ----------- | ------------- | ----------------------------------- |
| order_id    | INT           | Primary Key                         |
| customer_id | INT           | Foreign Key → customers.customer_id |
| order_date  | DATE          | Date of order                       |
| amount      | DECIMAL(10,2) | Order Amount                        |

OP

| customer_id | name    | email                                         |
| ----------- | ------- | --------------------------------------------- |
| 1           | Alice   | [alice@gmail.com](mailto:alice@gmail.com)     |
| 2           | Bob     | [bob@gmail.com](mailto:bob@gmail.com)         |
| 3           | Charlie | [charlie@gmail.com](mailto:charlie@gmail.com) |
| 4           | David   | [david@gmail.com](mailto:david@gmail.com)     |
| 5           | Eva     | [eva@gmail.com](mailto:eva@gmail.com)         |

**Solution :**
```
-- Solution 01:
SELECT c.customer_id,
       c.customer_name
FROM customers c
WHERE c.customer_id NOT IN (
    SELECT customer_id
    FROM orders
);
```
```
-- Solution 02:
SELECT c.customer_id,
       c.customer_name
FROM customers c
WHERE NOT EXISTS (
    SELECT o.customer_id
    FROM orders o
    WHERE o.customer_id = c.customer_id
);
```
```
-- Solution 03:
SELECT c.customer_id,
       c.customer_name
FROM customers c
LEFT JOIN orders o
    ON c.customer_id = o.customer_id
WHERE o.customer_id IS NULL;
```
```
Follow-up 1: NOT IN vs NOT EXISTS
Questions
What happens if you use NOT IN?
Why does it fail?
Why is NOT EXISTS considered NULL-safe?
Concept Tested:
Three-valued logic (TRUE, FALSE, UNKNOWN)
NULL handling
Correlated subqueries
#-------------------------
Follow-up 2: Why NOT EXISTS Doesn't Compare Values
Questions
Why don't we write: customer_id NOT EXISTS (...)
What is the purpose of: o.customer_id = c.customer_id
Concept Tested:
Correlated subqueries
EXISTS semantics
#--------------------------
Follow-up 3: Why SELECT 1?
SELECT 1 FROM orders
Questions : Why SELECT 1? Why not SELECT *? Does it affect performance?
Concept Tested: EXISTS optimization, SQL engine behavior
#--------------------------
Follow-up 4: LEFT JOIN vs NOT EXISTS
Questions
Do they always produce the same result? Which one would you choose? Why?

Expected Discussion
NOT EXISTS clearly expresses the intent of checking for missing related rows.
LEFT JOIN ... IS NULL is useful when you also need columns from the joined table.
Modern optimizers often generate similar execution plans.

Concept Tested: Readability, Query optimization, Anti-join patterns

#--------------------------
Follow-up 5: Duplicate Orders

Suppose the orders table contains: customer_id [1,1,2,3]
Questions : Will NOT EXISTS still work? Do duplicate rows affect the result?
Answer : Yes. NOT EXISTS only checks whether at least one matching row exists. Duplicates don't change the result.
Concept Tested:EXISTS semantics
```
```
Key Learnings from Question 1 : 
NOT IN vs NOT EXISTS
NULL handling in SQL
Three-valued logic (TRUE, FALSE, UNKNOWN)
Correlated subqueries
Why SELECT 1 is used with EXISTS
LEFT JOIN ... IS NULL as an anti-join
Effect of duplicate rows on EXISTS
Indexing for performance
Batch vs incremental processing in Data Engineering
Adapting SQL to changing business requirements
```
======================================
======================================

**Q2. Find the Second Highest Salary**

```
# ONLY SALARY
SELECT MAX(salary) AS second_highest_salary
FROM employee
WHERE salary < (
    SELECT MAX(salary)
    FROM employee
);
```
```
# EMP name and salary
SELECT *
FROM employee
WHERE salary = ( SELECT MAX(salary) FROM employee WHERE salary <
                     ( SELECT MAX(salary)
                      FROM employee
                     )
              );
```
```
SELECT emp_id,
       department,
       salary
FROM (
    SELECT emp_id,
           department,
           salary,
           DENSE_RANK() OVER(ORDER BY salary DESC) AS rnk
    FROM employee
) t
WHERE rnk = 2;
```

======================================
======================================

**Question 3: Top Selling Products**
```
orders(
    order_id,
    product_id,
    quantity,
    unit_price,
    order_date
)

```
* Follow up questions:
* 1. what is the product is repeating in the table
 ```
WITH product_revenue AS (
    SELECT
        product_id,
        SUM(quantity * unit_price) AS total_revenue
    FROM order_items
    GROUP BY product_id
),

ranked_products AS (
    SELECT
        p.category,
        p.product_name,
        pr.total_revenue,
        DENSE_RANK() OVER (
            PARTITION BY p.category
            ORDER BY pr.total_revenue DESC
        ) AS rnk
    FROM products p
    JOIN product_revenue pr
        ON p.product_id = pr.product_id
)

SELECT
    category,
    product_name,
    total_revenue
FROM ranked_products
WHERE rnk <= 3
ORDER BY category, rnk;
```


======================================
======================================
**SQL Interview #4 — Find Duplicate Customer Emails**

| Column        | Description    |
| ------------- | -------------- |
| customer_id   | Primary Key    |
| customer_name | Customer Name  |
| email         | Customer Email |

OP
| email                                     |
| ----------------------------------------- |
| [alice@gmail.com](mailto:alice@gmail.com) |
| [bob@gmail.com](mailto:bob@gmail.com)     |

**Q 00 : Find Duplicate Customer Emails**
```
SELECT email FROM customers GROUP BY email HAVING count(*) > 1
```

**Follow Up 01 : Show each duplicate email along with the number of times it appears.**
```
SELECT email, count(*) as frequency FROM customers GROUP BY email HAVING count(*) > 1
```
**Follow Up 02 : Show the complete details of customers whose email is duplicated.**
```
SELECT customer_id, customer_name, email 
FROM customers 
WHERE email IN (SELECT email FROM customers GROUP BY email HAVING count(*) > 1)
```
**Follow Up 03 : Solve the previous question without using IN.**
```
SELECT customer_id, customer_name, email 
FROM customers c1 
WHERE EXISTS (SELECT email FROM customers c2 WHERE c1.email = c2.email)
```
**Follow Up 04 : Some email values are NULL. Should two NULL values be considered duplicates?**
```
SELECT  customer_id, 
        customer_name, 
        email 
FROM customers 
WHERE email IN (
        SELECT email 
        FROM customers 
        WHERE email IS NOT NULL 
        GROUP BY email 
        HAVING count(*) > 1
        )
```
**Follow Up 05 : Emails differ only by letter case** \
Example:\
abc@gmail.com
ABC@gmail.com
Abc@gmail.com     Should these be treated as duplicates? \
Yes, if the business considers email addresses case-insensitive, convert them to the same case before grouping.
```
SELECT LOWER(email) AS email,
       COUNT(*) AS frequency
FROM customers
WHERE email IS NOT NULL
GROUP BY LOWER(email)
HAVING COUNT(*) > 1;       
```

**Follow Up 06 : Return only one customer record for each duplicate email.**
```
WITH ranked_customer_details AS (
    SELECT customer_id,
           customer_name,
           email,
           ROW_NUMBER() OVER (
               PARTITION BY email
               ORDER BY customer_id
           ) AS rnk
    FROM customers
    WHERE email IN (
        SELECT email
        FROM customers
        WHERE email IS NOT NULL
        GROUP BY email
        HAVING COUNT(*) > 1
    )
)

SELECT customer_id,
       customer_name,
       email
FROM ranked_customer_details
WHERE rnk = 1;
```
**Follow Up 07 : The customers table has 100 million rows.**
| Scenario                 | Best Approach                    |
| ------------------------ | -------------------------------- |
| Small table (10K rows)   | Full table scan                  |
| Medium table (1M rows)   | Full scan is usually acceptable  |
| Large table (100M+ rows) | Incremental processing           |
| Daily ETL                | Process only new records         |
| Dashboard queries        | Query precomputed summary tables |

```
If an interviewer asks: How would you optimize this query?
A strong answer is:
"For a table with 100 million rows, the SQL query is correct, but I wouldn't scan the entire table 
every day if only a small amount of new data is added. I'd prefer an incremental ETL approach that
processes only new or changed records and maintains a summary table with email counts. This reduces
compute time and makes the pipeline scalable."
```
```
"How would you handle duplicate email detection for a 100-million-row table?"

A strong answer is:
"I wouldn't run a full GROUP BY on the entire table every day. I'd maintain a summary table containing
 each email and its count. During the daily ETL, I'd aggregate only the new customer records and MERGE
 those counts into the summary table. To identify duplicates, I'd simply query WHERE email_count > 1.
This incremental approach scales much better than rescanning the full dataset."

This is the kind of answer that demonstrates both SQL knowledge and Data Engineering thinking.
```
**Follow Up 08 : You need to identify duplicate emails every day in an ETL pipeline. Would you recompute everything daily, or use an incremental approach?**
Tests:
Batch vs Incremental Processing
ETL Design
Scalability



======================================
======================================

**SQL Interview Question 5: Month-over-Month Sales Growth**

| Column     | Type    |
| ---------- | ------- |
| order_id   | INT     |
| order_date | DATE    |
| amount     | DECIMAL |

| order_id | order_date | amount |
| -------- | ---------- | -----: |
| 1        | 2025-01-05 |    100 |
| 2        | 2025-01-20 |    200 |
| 3        | 2025-02-10 |    500 |
| 4        | 2025-02-18 |    300 |
| 5        | 2025-03-15 |    600 |
| 6        | 2025-03-25 |    400 |

OP
| Month   | Sales | Previous Month Sales | Growth % |
| ------- | ----: | -------------------: | -------: |
| 2025-01 |   300 |                 NULL |     NULL |
| 2025-02 |   800 |                  300 |   166.67 |
| 2025-03 |  1000 |                  800 |    25.00 |

Formula : ((Current Month Sales - Previous Month Sales) / Previous Month Sales) * 100

**Q05. Month-over-Month Sales Growth**

```
WITH monthlyrev AS
(
    SELECT
        YEAR(order_date) AS yr,
        MONTH(order_date) AS mmonth,
        SUM(amount) AS current_month_sale
    FROM orders
    GROUP BY YEAR(order_date),
             MONTH(order_date)
),

reqdata AS
(
    SELECT
        yr,
        mmonth,
        current_month_sale AS CM,
        LAG(current_month_sale)
        OVER(
            ORDER BY yr,mmonth
        ) AS PM
    FROM monthlyrev
)

SELECT
    yr,
    mmonth,
    CM,
    PM,
    ((CM-PM)*100.0)/NULLIF(PM,0) AS growth_percentage
FROM reqdata;
```
```
Things to Learn:
1. Use of LAG() function, which takes 1 argument
2. Deal with the 1st Row where LAG value will be null, so use cases.
```

======================================
======================================
**SQL Interview Question 6: Running Total per Customer**
```
| Column      | Type          |
| ----------- | ------------- |
| order_id    | INT           |
| customer_id | INT           |
| order_date  | DATE          |
| amount      | DECIMAL(10,2) |
```
```
SELECT customer_id,
       order_date,
       amount,
       SUM(amount) OVER(
           PARTITION BY customer_id
           ORDER BY order_date
       ) AS running_total
FROM orders;
```
```
Whenever you see a window function, train yourself to ask two questions:
Which rows belong to the partition? :  (PARTITION BY customer_id)
Which rows belong to the frame? : 
(UNBOUNDED PRECEDING → CURRENT ROW)
(1 PRECEDING → CURRENT ROW)
(2 PRECEDING → CURRENT ROW)
etc.
```
```
"Where does SQL know to start summing from?" The answer is: because of the default window frame.

When you specify an ORDER BY inside a window function, SQL behaves as if you had written:
SUM(amount)
OVER (
    PARTITION BY customer_id
    ORDER BY order_date
    ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
)

Let's break that down.
UNBOUNDED PRECEDING = start from the first row in this partition.
CURRENT ROW = stop at the current row.
```
| Requirement     | Window Function                          |
| --------------- | ---------------------------------------- |
| Previous row    | `LAG()`                                  |
| Next row        | `LEAD()`                                 |
| Running total   | `SUM() OVER (...)`                       |
| Running average | `AVG() OVER (...)`                       |
| Ranking         | `ROW_NUMBER()`, `RANK()`, `DENSE_RANK()` |


======================================
======================================

**Question 7: Top 3 Highest Paid Employees from Each Department**

EMPLOYEE :
    emp_id INT,
    emp_name VARCHAR(50),
    department VARCHAR(50),
    salary INT

```
Interviewer: "Find the top 3 highest-paid employees in each department."
Strong Candidate:
"Before I start, I'd like to clarify one requirement. If two employees have the same salary and they're tied at the 3rd position, should I return both employees, or should I return exactly 3 employees per department?"

That question tells the interviewer you understand that the choice between ROW_NUMBER(), RANK(), and DENSE_RANK() depends on the business requirement—not just SQL syntax.
if yes ties allowed  = dense_rank()
Exactly 3 =  row_number()
```
```
SELECT *
FROM (
    SELECT emp_id,
           emp_name,
           department,
           salary,
           ROW_NUMBER() OVER (
               PARTITION BY department
               ORDER BY salary DESC
           ) AS rnk
    FROM employees
) t
WHERE rnk <= 3;
```
======================================
======================================

**Question 8: Find Customers Who Purchased on Consecutive Days**

orders 
    order_id INT,
    customer_id INT,
    order_date DATE
```
SELECT *
FROM (
    SELECT customer_id,
           order_date,
           CASE
               WHEN LEAD(order_date) OVER (
                       PARTITION BY customer_id
                       ORDER BY order_date
                    ) - order_date = INTERVAL '1 day'
               THEN 1
               ELSE 0
           END AS cons
    FROM orders
) AS t
WHERE cons = 1;
```
```
Follow Up : Q. Suppose the table contain

customer_id	order_date
101	2025-01-01
101	2025-01-01
101	2025-01-02

Would your current query still produce the correct answer? If not, how would you modify it?

WITH uniqueorders AS (
    SELECT DISTINCT customer_id, order_date
    FROM orders
)

SELECT DISTINCT customer_id
FROM (
    SELECT customer_id,
           order_date,
           CASE
               WHEN LEAD(order_date) OVER (
                        PARTITION BY customer_id
                        ORDER BY order_date
                    ) - order_date = INTERVAL '1 day'
               THEN 1
               ELSE 0
           END AS cons
    FROM uniqueorders
) t
WHERE cons = 1;

```
======================================
======================================
**Question 9: First and Last Order for Each Customer** \
```
orders
    order_id,
    customer_id,
    order_date,
    amount
```
```
SELECT customer_id,
       MIN(order_date) AS first_order,
       MAX(order_date) AS last_order
FROM orders
GROUP BY customer_id;
```
```
SELECT customer_id,
       order_date,
       FIRST_VALUE(order_date) OVER(PARTITION BY customer_id ORDER BY order_date) AS first_order,
       LAST_VALUE(order_date) OVER(PARTITION BY customer_id ORDER BY order_date) AS last_order
FROM orders;
-- above query is WRONG Thats not how it works

SELECT customer_id,
       order_date,
       FIRST_VALUE(order_date) OVER (PARTITION BY customer_id ORDER BY order_date) AS first_order,

       LAST_VALUE(order_date) OVER (PARTITION BY customer_id ORDER BY order_date ROWS BETWEEN UNBOUNDED PRECEDING
              AND UNBOUNDED FOLLOWING ) AS last_order
FROM orders;
```
### AND WHY WE DO THIS HAS THE INTERESTING ANSWER.

```
ROWS BETWEEN UNBOUNDED PRECEDING
AND UNBOUNDED FOLLOWING

Means :  "For every row, use the entire partition—from the first row to the last row."
First row ------------------> Last row

ROWS BETWEEN UNBOUNDED PRECEDING
AND CURRENT ROW

Means :  "For every row, use the entire partition—from the first row to current row."
First row  ----------------->  Me
```
```
Interview Tip ⭐

If an interviewer asks:
"Why isn't LAST_VALUE() returning the last value?"
The expected answer is:

"Because the default window frame ends at the current row. LAST_VALUE() returns the last row within the current frame, not necessarily the last row in the partition. To get the true last row, I need ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING."
```
======================================
======================================

**Question 10: Highest Paid Employee(s) in Each Department**

```
SELECT employee_id,
       employee_name,
       department,
       salary
FROM (
    SELECT employee_id,
           employee_name,
           department,
           salary,
           DENSE_RANK() OVER (
               PARTITION BY department
               ORDER BY salary DESC
           ) AS rnk
    FROM employees
) t
WHERE rnk = 1;
```
