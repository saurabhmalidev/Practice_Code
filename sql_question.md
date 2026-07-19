**Q1. Customers with Zero Orders**
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
"For a table with 100 million rows, the SQL query is correct, but I wouldn't scan the entire table every day if only a small amount of new data is added. I'd prefer an incremental ETL approach that processes only new or changed records and maintains a summary table with email counts. This reduces compute time and makes the pipeline scalable."
```
```
"How would you handle duplicate email detection for a 100-million-row table?"

A strong answer is:
"I wouldn't run a full GROUP BY on the entire table every day. I'd maintain a summary table containing each email and its count. During the daily ETL, I'd aggregate only the new customer records and MERGE those counts into the summary table. To identify duplicates, I'd simply query WHERE email_count > 1. This incremental approach scales much better than rescanning the full dataset."

This is the kind of answer that demonstrates both SQL knowledge and Data Engineering thinking.
```
**Follow Up 08 : You need to identify duplicate emails every day in an ETL pipeline. Would you recompute everything daily, or use an incremental approach?**
Tests:
Batch vs Incremental Processing
ETL Design
Scalability




