# 💡 6) Second-Highest Salary in Each Department: Solution

**Difficulty:** 🟡 Medium &nbsp;|&nbsp; **Concepts used:** CTE (WITH), DENSE_RANK(), PARTITION BY, filtering on a window function

> [📄 Question](Question.md) &nbsp;|&nbsp; 💡 **Solution** &nbsp;|&nbsp; [🏠 All questions](../README.md)

## 🧾 Solution Code (MySQL 8.0+)

```sql
WITH ranked AS (
    SELECT department,
           employee_id,
           salary,
           DENSE_RANK() OVER (PARTITION BY department
                              ORDER BY salary DESC) AS salary_rank
    FROM employees
)
SELECT department,
       employee_id,
       salary
FROM ranked
WHERE salary_rank = 2
ORDER BY department, employee_id;
```

## 🪜 Step-by-Step Explanation

**Step 1: rank salaries inside each department (`DENSE_RANK`)**

```sql
DENSE_RANK() OVER (PARTITION BY department ORDER BY salary DESC) AS salary_rank
```
- `OVER (…)` makes `DENSE_RANK()` a **window function**: it adds a number to every row without removing any rows.
- `PARTITION BY department` restarts the ranking for each department, so Finance and Sales are ranked separately.
- `ORDER BY salary DESC` ranks the highest salary as 1.
- `DENSE_RANK` gives **equal salaries the same rank** and does **not skip** the next number.

This is what the `ranked` step produces:

```text
+------------+-------------+--------+-------------+
| department | employee_id | salary | salary_rank |
+------------+-------------+--------+-------------+
| Finance    | C           |   6000 |           1 |
| Finance    | A           |   5000 |           2 |
| Finance    | B           |   4500 |           3 |
| Sales      | E           |   8000 |           1 |
| Sales      | F           |   7000 |           2 |
| Sales      | G           |   7000 |           2 |
| Sales      | D           |   4000 |           3 |
| Sales      | H           |   3000 |           4 |
+------------+-------------+--------+-------------+
```

F and G both earn 7000, so both get rank 2.

**Step 2: keep only rank 2**
- `WITH ranked AS ( … )` is a **CTE** (Common Table Expression): a named temporary result we can query like a table.
- We need it because a window function can't be used directly in `WHERE`. `WHERE` runs before the window function is calculated. So we calculate the rank in the CTE first, then filter with `WHERE salary_rank = 2` outside it.

**Why `DENSE_RANK` and not `RANK` or `ROW_NUMBER`?**

| Function | Sales salaries 8000, 7000, 7000, 4000 | Problem |
|---|---|---|
| `ROW_NUMBER()` | 1, 2, 3, 4 | gives F and G different numbers, so only one of them would be shown |
| `RANK()` | 1, 2, 2, 4 | works here, but if two people tied for *first*, ranks would be 1, 1, 3 and there would be **no rank 2** at all |
| `DENSE_RANK()` | 1, 2, 2, 3 | always means "the 2nd-highest *distinct* salary", and keeps ties ✅ |

---

[⬅ Back to the question](Question.md) &nbsp;|&nbsp; [🏠 All questions](../README.md)
