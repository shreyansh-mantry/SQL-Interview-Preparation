# 💡 10) Funnel Conversion by Stage: Solution

**Difficulty:** 🟡 Medium &nbsp;|&nbsp; **Concepts used:** CTEs, CASE (simple form), COUNT(DISTINCT), FIRST_VALUE(), LAG(), ROUND()

> [📄 Question](Question.md) &nbsp;|&nbsp; 💡 **Solution** &nbsp;|&nbsp; [🏠 All questions](../README.md)

## 🧾 Solution Code (MySQL 8.0+)

```sql
-- Step 1: count users per stage + give each stage its funnel position
WITH stage_counts AS (
    SELECT stage,
           CASE stage
               WHEN 'Signup'          THEN 1
               WHEN 'Account Started' THEN 2
               WHEN 'Account Created' THEN 3
               WHEN 'Purchase Done'   THEN 4
           END                     AS stage_order,
           COUNT(DISTINCT user_id) AS users
    FROM funnel_events
    GROUP BY stage
),

-- Step 2: window functions bring the baseline numbers in as columns
windowed AS (
    SELECT stage,
           stage_order,
           users,
           FIRST_VALUE(users) OVER (ORDER BY stage_order) AS signup_users,     -- top of funnel (7)
           LAG(users)         OVER (ORDER BY stage_order) AS prev_stage_users  -- previous stage count
    FROM stage_counts
)

-- Step 3: division is plain arithmetic on columns
SELECT stage,
       users,
       ROUND(100.0 * users / signup_users,     1) AS pct_of_total,
       ROUND(100.0 * users / prev_stage_users, 1) AS step_conversion
FROM windowed
ORDER BY stage_order;
```

## 🪜 Step-by-Step Explanation

**Step 1: count users per stage and give each stage a position (`stage_counts`)**
- `GROUP BY stage` makes one bucket per stage.
- `COUNT(DISTINCT user_id)` counts **unique** users in each bucket. `DISTINCT` makes sure a user who logged the same stage twice is counted once.
- Stage names sort alphabetically (*Account Created* before *Signup*), which is **not** funnel order. So `CASE stage WHEN 'Signup' THEN 1 …` gives each stage a number (`stage_order`) we can sort by.

**Step 2: put the comparison numbers on every row (`windowed`)**
- `FIRST_VALUE(users) OVER (ORDER BY stage_order)` looks at the rows sorted by funnel order and copies the **first** row's `users` (Signup = 7) onto every row. That's the denominator for `pct_of_total`.
- `LAG(users) OVER (ORDER BY stage_order)` copies the `users` value from the **previous** row. The first row has no previous row, so it gets **NULL**. That's the denominator for `step_conversion`.

```text
+-----------------+-------------+-------+--------------+------------------+
| stage           | stage_order | users | signup_users | prev_stage_users |
+-----------------+-------------+-------+--------------+------------------+
| Signup          |           1 |     7 |            7 |             NULL |
| Account Started |           2 |     5 |            7 |                7 |
| Account Created |           3 |     4 |            7 |                5 |
| Purchase Done   |           4 |     2 |            7 |                4 |
+-----------------+-------------+-------+--------------+------------------+
```

**Step 3: divide and round**
- `100.0 * users / signup_users` gives the percentage of all signups: e.g. Account Started = 100 × 5 / 7 = 71.43.
- `100.0 * users / prev_stage_users` gives the step conversion: e.g. Account Created = 100 × 4 / 5 = 80.
- `ROUND(x, 1)` rounds to 1 decimal place.
- For Signup, `prev_stage_users` is NULL, and **any arithmetic with NULL returns NULL**. That's why step_conversion is NULL on the first row, as the question requires.
- Writing `100.0` (a decimal) is a good habit. In MySQL `5 / 7` already returns a decimal, but in some databases (e.g. PostgreSQL, SQL Server) integer ÷ integer drops the decimals and would give 0.

---

[⬅ Back to the question](Question.md) &nbsp;|&nbsp; [🏠 All questions](../README.md)
