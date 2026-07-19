**Q. Customers with Zero Orders**
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
SELECT c.customer_id,
       c.customer_name
FROM customers c
WHERE c.customer_id NOT IN (
    SELECT customer_id
    FROM orders
);
```
```
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
