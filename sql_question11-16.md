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

**Question 13 — Consecutive Monthly Purchases (Gaps & Islands)**

| Column       | Data Type     |
| ------------ | ------------- |
| order_id     | INT           |
| customer_id  | INT           |
| order_date   | DATE          |
| order_amount | DECIMAL(10,2) |




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

