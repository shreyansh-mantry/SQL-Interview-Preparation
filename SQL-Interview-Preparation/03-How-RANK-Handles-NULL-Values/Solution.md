# 💡 3) How RANK() Handles NULL Values: Solution

**Difficulty:** 🟢 Easy &nbsp;|&nbsp; **Concepts used:** RANK() window function, OVER (ORDER BY …), NULL sort order, IS NULL

> [📄 Question](Question.md) &nbsp;|&nbsp; 💡 **Solution** &nbsp;|&nbsp; [🏠 All questions](../README.md)

## 🧾 Solution Code (MySQL 8.0+)

**(a) Highest salary first**

```sql
SELECT department,
       salary,
       RANK() OVER (ORDER BY salary DESC) AS salary_rank
FROM department_salary
ORDER BY salary_rank, department;
```

**(b) Lowest salary first**

```sql
SELECT department,
       salary,
       RANK() OVER (ORDER BY salary ASC) AS salary_rank
FROM department_salary
ORDER BY salary_rank, department;
```

**(c) Lowest first, NULLs at the bottom**

```sql
SELECT department,
       salary,
       RANK() OVER (ORDER BY salary IS NULL, salary ASC) AS salary_rank
FROM department_salary
ORDER BY salary_rank, department;
```

## 🪜 Step-by-Step Explanation

**The functions used**
- **`RANK() OVER (ORDER BY salary DESC)`** is a *window function*. It doesn't group or remove rows. It adds a rank number to every row, based on the order given inside `OVER (…)`.
- Rows with the **same value tie** and get the **same rank**. The next rank then **skips** numbers: two rows tied at 1 are followed by rank 3. (`DENSE_RANK()` would not skip; it would give 3 rows ranks 1, 1, 2.)
- The final `ORDER BY salary_rank, department` only sorts the displayed output. It doesn't affect the ranking.

**How MySQL treats NULL when sorting**

`RANK()` doesn't ignore NULL rows. It ranks them wherever the sort puts them. In **MySQL, NULL is treated as the lowest possible value**:
- `ORDER BY salary DESC`: NULLs come **last**.
- `ORDER BY salary ASC`: NULLs come **first**.

All NULLs sort as equal to each other, so they **tie** and share one rank.

**(a) DESC:** Business 1500 → 1, Finance 1000 → 2, Sales 500 → 3, then HR and Marketing (both NULL) tie at **4**.

**(b) ASC:** HR and Marketing (NULL) come first and tie at **1**. Because two rows share rank 1, the next rank is **3** (Sales), then Finance 4, Business 5.

**(c) NULLs at the bottom:** MySQL has no `NULLS LAST` keyword, so we sort on two things:
1. `salary IS NULL` returns **0** for a real salary and **1** for NULL. Sorting it ascending puts every real salary (0) before every NULL (1).
2. `salary ASC` then sorts the real salaries from lowest to highest.

Result: Sales 1, Finance 2, Business 3, and the NULLs tie at 4.

> **Interview note:** PostgreSQL and Oracle do the opposite. They treat NULL as the *highest* value, so NULLs come *first* in `DESC`, and they support `NULLS FIRST` / `NULLS LAST`. Always check which database the interviewer means.

---

[⬅ Back to the question](Question.md) &nbsp;|&nbsp; [🏠 All questions](../README.md)
