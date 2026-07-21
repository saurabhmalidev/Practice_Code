**Question 17 — Employees Earning More Than Their Manager (Self Join)**

| emp_id | emp_name | manager_id | salary |
| -----: | -------- | ---------: | -----: |
|      1 | Alice    |       NULL | 100000 |
|      2 | Bob      |          1 |  90000 |
|      3 | Charlie  |          1 | 120000 |
|      4 | David    |          2 |  95000 |
|      5 | Eva      |          2 |  85000 |

* Fundaamental rule of the Self Join:
  1. Always name the table properly \
     employee : emp \
     employee : manager \
  3. Sequence of the join condition matters and read them properly.\ 
     like {emp.manager_id == manager.emp_id} Read aloud : """The employee's manager id is equals to manager's employee id"""
```
SELECT
    emp.emp_id,
    emp.emp_name,
    emp.salary as employee_salary,
    manager.salary as manager_salary
FROM employee emp
JOIN employee manager
    ON emp.manager_id = manager.emp_id
WHERE emp.salary > manager.salary;
```
=====================================
=====================================
**Question 18 — Pivot Sales by Month**


Question 19 — Median Salary

Question 20 — Products Purchased Together (Self Join)
