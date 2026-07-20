**Question 11 — Nth Highest Salary (Parameterized)**

| Column     | Data Type |
| ---------- | --------- |
| emp_id     | INT       |
| emp_name   | VARCHAR   |
| department | VARCHAR   |
| salary     | INT       |

```
SELECT salary
FROM (
    SELECT salary,
           DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk
    FROM employee
) t
WHERE rnk = :N;
```
```
SELECT employee_id, salary
FROM (
    SELECT employee_id,
           salary,
           DENSE_RANK() OVER (ORDER BY salary DESC) AS salary_rank
    FROM employee
) t
WHERE salary_rank = :N
ORDER BY employee_id
LIMIT 1;
```


**Question 12 — Customers Who Purchased Every Month**

| Column       | Data Type     |
| ------------ | ------------- |
| order_id     | INT           |
| customer_id  | INT           |
| order_date   | DATE          |
| order_amount | DECIMAL(10,2) |


```
SELECT customer_id, total_month
FROM (
    SELECT customer_id,
           COUNT(DISTINCT MONTH(order_date)) AS total_month
    FROM orders
    WHERE order_date >= '2025-01-01'
      AND order_date < '2026-01-01'
    GROUP BY customer_id
) AS t
WHERE total_month = 12;
```

**Question 13 — Consecutive Monthly Purchases (Gaps & Islands). Identify customers who purchased in 3 or more consecutive months during 2025**

Orders
| Column       | Data Type     |
| ------------ | ------------- |
| order_id     | INT           |
| customer_id  | INT           |
| order_date   | DATE          |
| order_amount | DECIMAL(10,2) |

```
WITH monthly_orders AS (
    SELECT DISTINCT customer_id, MONTH(order_date) AS month_no
    FROM orders WHERE order_date >= '2025-01-01' AND order_date < '2026-01-01'
),

islandcte AS (
    SELECT
        customer_id, month_no,
        month_no - ROW_NUMBER() OVER ( PARTITION BY customer_id ORDER BY month_no ) AS island
    FROM monthly_orders
)

SELECT
    customer_id,
    COUNT(*) AS freq
FROM islandcte
GROUP BY customer_id, island
HAVING COUNT(*) >= 3;
```


=====================================
=====================================

**Question 14 — Running Balance (Window Aggregation)**

Sample Table
| transaction_id | account_id | transaction_date | transaction_type | amount |
| -------------- | ---------- | ---------------- | ---------------- | -----: |
| 1              | 101        | 2025-01-01       | Credit           |   1000 |
| 2              | 101        | 2025-01-03       | Debit            |    200 |
| 3              | 101        | 2025-01-05       | Credit           |    500 |
| 4              | 101        | 2025-01-07       | Debit            |    100 |
| 5              | 102        | 2025-01-02       | Credit           |    800 |
| 6              | 102        | 2025-01-04       | Debit            |    300 |

Expected Output
| account_id | transaction_date | transaction_type | amount | running_balance |
| ---------- | ---------------- | ---------------- | -----: | --------------: |
| 101        | 2025-01-01       | Credit           |   1000 |            1000 |
| 101        | 2025-01-03       | Debit            |    200 |             800 |
| 101        | 2025-01-05       | Credit           |    500 |            1300 |
| 101        | 2025-01-07       | Debit            |    100 |            1200 |
| 102        | 2025-01-02       | Credit           |    800 |             800 |
| 102        | 2025-01-04       | Debit            |    300 |             500 |

```
# What i thought
1st we will use the case condition and inside case
      if we have credit then lag(running balance) + amount = running balance
      if we have debit then lag(runnning balance) - amount = running balance
2nd handle the 1st row, remaining will be managed.

# BUT problem with this is Where does running_balance come from? You're trying to compute it, but at the same time you're using it inside LAG().It's a circular dependency.

WHY : LAG() looks at an existing column, not one you're creating in the same SELECT.

# Another thought we will assign sign(+ve or -ve) to the the amount based on credit or debit and then we will find the running total using teh sum function with windows
```
```
with amountwithsign as (
  select  account_id, transaction_id, transaction_date, 
          transaction_type, amount,
          case
            when transaction_type = Credit then amount
            else -amount
          end as amountsign
        from transaction
)
select  account_id, transaction_id, transaction_date, 
        transaction_type, amount,
        SUM(amountsign) OVER(PARTITON BY account_id order by transaction_id, transaction_date) as running_total from amountwithsign
```

==============================
==============================


**Question Bonus : Write a query where user logged in for 5 or more days**

User_logins
    user_id
    login_date
```
SELECT * FROM 
(select  user_id, login_date,
        CASE
          when 
            (lead(login_date)over(partition by user_id order by login_date) - login_date) = 1 and
            (lead(login_date , 2)over(partition by user_id order by login_date) - login_date) = 2 and
            (lead(login_date , 3)over(partition by user_id order by login_date) - login_date) = 3 and
            (lead(login_date , 4)over(partition by user_id order by login_date) - login_date) = 4
              then 1
          else
            0
        END as flag
  FROM user_logins) as t 
WHERE flag = 1
```
```
# NOTE :  First though came into my mind is we use the case when lead(col, 1/2/3/4) - login_date = 1/2/3/4
But in this case we have to write the repeatativ elogic when we try to deal with the q qhere we are asked consecutive or streak check pattern

# Solution :  TO deal with this we have GAPS and ISLAND strategy where we creates an island identifier.
| user_id | login_date | island |
| ------- | ---------- | ------ |
| 1       | Jan1       | Dec31  |
| 1       | Jan2       | Dec31  |
| 1       | Jan3       | Dec31  |
| 1       | Jan5       | Jan1   |
| 1       | Jan6       | Jan1   |

Notice here Notice that the first three rows have the same island. That means they belong to one streak. The next two rows have another island. That means they belong to another streak.
So what do we group by? Exactly those columns that define one streak.
* user_id (because each user's streaks are separate)
* island (because each island is one streak)
```
```
WITH cte AS (
    SELECT
        user_id,
        login_date,
        login_date - ROW_NUMBER() OVER (PARTITION BY user_id ORDER BY login_date ) AS island
    FROM user_logins
)
SELECT
    user_id,
    island,
    COUNT(*) AS consecutive_days
FROM cte
GROUP BY
    user_id,
    island
HAVING consecutive_days >= 5;
```

==================================
==================================



















