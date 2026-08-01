**Q. Find customers who never placed an order.**
| customer_id | name    |
| ----------- | ------- |
| 1           | Alice   |
| 2           | Bob     |
| 3           | Charlie |
| 4           | David   |

| order_id | customer_id |
| -------- | ----------- |
| 101      | 1           |
| 102      | 1           |
| 103      | 2           |

NOTE : Solving problem using LEFT JOIN
```
SELECT c.customer_id
FROM customers c
LEFT JOIN orders o
ON c.customer_id = o.customer_id          
WHERE o.customer_id IS NULL;
```
====================================
====================================

**Find customers who have never placed a completed order.**
| customer_id |
| ----------- |
| 1           |
| 2           |
| 3           |
| 4           |

| order_id | customer_id | status    |
| -------- | ----------- | --------- |
| 101      | 1           | Completed |
| 102      | 1           | Cancelled |
| 103      | 2           | Cancelled |

```
# Wrong Answer
SELECT ...
FROM customers c
LEFT JOIN orders o
ON c.customer_id = o.customer_id
WHERE o.status != 'Completed';
```
```
SELECT c.customer_id
FROM customers c
LEFT JOIN orders o
ON c.customer_id = o.customer_id
AND o.status = 'Completed'    # This condition will return True where order status is Completer and remaining NULL.
WHERE o.customer_id IS NULL;
```
```
#WHY
| customer_id | status    | Condition |
| ----------- | --------- | --------- |
| 1           | Completed | FALSE     |
| 1           | Cancelled | TRUE ✅    |
| 2           | Cancelled | TRUE ✅    |
| 3           | NULL      | UNKNOWN ❌ |
| 4           | NULL      | UNKNOWN ❌ |

Final result:
customer_id
1
2
But this is wrong.
Why?
Customer 1 has placed a completed order. They should not appear in the answer.
---------
Execute it mentally
For Customer 1:
Order 1 → Completed → Match ✅
Order 2 → Cancelled → Doesn't satisfy the ON condition ❌
At least one match exists, so no NULL row is created.
```

DEF/ IMP : For every row in the left table, SQL compares it with every row in the right table and 
evaluates the ON condition for each pair. After scanning all right-table rows, if at least one pair 
satisfies the ON condition, SQL outputs all matching rows. If no pair satisfies the ON condition, then 
a LEFT JOIN outputs exactly one row with NULL values for the right table.

================================
================================


