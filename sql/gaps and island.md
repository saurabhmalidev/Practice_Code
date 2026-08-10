**Q. Consecutive Monthly Purchases (Gaps & Islands). Identify customers who purchased in 3 or more consecutive months during 2025**
```
Orders
| Column       | Data Type     |
| ------------ | ------------- |
| order_id     | INT           |
| customer_id  | INT           |
| order_date   | DATE          |
| order_amount | DECIMAL(10,2) |



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

**Q. Find the Consecutive Login Streak where employee logged in.** \
Similar Q : Consecutive Numbers" (LeetCode 180) or "Stadium Traffic" (LeetCode 601). \
Jobs : asked in EY
```
|'success' | '2025-08-01')|
|'success' | '2025-08-02')|
|'fail'    | '2025-08-03' |
|'fail'    |'2025-08-04') |
|'success' | '2025-08-13')|

expected output
+------------+---------------+---------------+
| job_status | min(job_date) | max(job_date) |
+------------+---------------+---------------+
| fail       | 2025-08-03    | 2025-08-04    |
| success    | 2025-08-01    | 2025-08-02    |
| success    | 2025-08-13    | 2025-08-13    |
+------------+---------------+---------------+
```
```
CREATE TABLE jobs (
  job_status TEXT NOT NULL,
  job_date date 
);
INSERT INTO jobs VALUES ('success', '2025-08-01');
INSERT INTO jobs VALUES ('success', '2025-08-02');
INSERT INTO jobs VALUES ('fail', '2025-08-03');
INSERT INTO jobs VALUES ('fail', '2025-08-04');
INSERT INTO jobs VALUES ('success', '2025-08-13');

with island as (select 
job_status,
job_date,
day(job_date) - row_number() over(partition by job_status order by day(job_date))  as island
from jobs)

select job_status, min(job_date), max(job_date) from island group by job_status,island ;
```
