# 💡 15) Monthly Cohort Retention Triangle: Solution

**Difficulty:** 🔴 Hard &nbsp;|&nbsp; **Concepts used:** Cohort analysis, MIN(), DATE_FORMAT(), CAST(), YEAR() / MONTH(), month-difference arithmetic, COUNT(DISTINCT), pivot with MAX(CASE), ROUND()

> [📄 Question](Question.md) &nbsp;|&nbsp; 💡 **Solution** &nbsp;|&nbsp; [🏠 All questions](../README.md)

## 🧾 Solution Code (MySQL 8.0+)

```sql
WITH
-- Step 1: each user's COHORT = month of first activity
first_activity AS (
    SELECT user_id,
           CAST(DATE_FORMAT(MIN(activity_date), '%Y-%m-01') AS DATE) AS cohort_month
    FROM user_activity
    GROUP BY user_id
),

-- Step 2: each DISTINCT month a user was active
user_months AS (
    SELECT DISTINCT
           user_id,
           CAST(DATE_FORMAT(activity_date, '%Y-%m-01') AS DATE) AS activity_month
    FROM user_activity
),

-- Step 3: attach the cohort + compute MONTH_NUMBER (0, 1, 2, 3)
cohort_activity AS (
    SELECT f.cohort_month,
           (YEAR(m.activity_month) * 12 + MONTH(m.activity_month))
         - (YEAR(f.cohort_month)   * 12 + MONTH(f.cohort_month))   AS month_number,
           m.user_id
    FROM user_months    m
    JOIN first_activity f ON m.user_id = f.user_id
),

-- Step 4: count users per cohort per month_number (long format)
retention_long AS (
    SELECT cohort_month,
           month_number,
           COUNT(DISTINCT user_id) AS retained_users
    FROM cohort_activity
    GROUP BY cohort_month, month_number
),

-- Step 5: each cohort's starting size (M0) = the denominator
cohort_size AS (
    SELECT cohort_month,
           retained_users AS total_users
    FROM retention_long
    WHERE month_number = 0
),

-- Step 6: pivot to M0..M3, turning M1-M3 into % of M0 (numbers)
pivoted AS (
    SELECT r.cohort_month,
           MAX(CASE WHEN r.month_number = 0 THEN r.retained_users END)                                   AS m0,
           ROUND(100.0 * MAX(CASE WHEN r.month_number = 1 THEN r.retained_users END) / s.total_users, 1) AS m1_pct,
           ROUND(100.0 * MAX(CASE WHEN r.month_number = 2 THEN r.retained_users END) / s.total_users, 1) AS m2_pct,
           ROUND(100.0 * MAX(CASE WHEN r.month_number = 3 THEN r.retained_users END) / s.total_users, 1) AS m3_pct
    FROM retention_long r
    JOIN cohort_size    s ON r.cohort_month = s.cohort_month
    GROUP BY r.cohort_month, s.total_users
)

-- Step 7: format the numbers as text with a % sign (80.0 -> '80%', 33.3 -> '33.3%')
SELECT DATE_FORMAT(cohort_month, '%Y-%m')               AS cohort_month,
       m0                                               AS M0_total_users,
       CONCAT(TRIM(TRAILING '.0' FROM m1_pct), '%')     AS M1,
       CONCAT(TRIM(TRAILING '.0' FROM m2_pct), '%')     AS M2,
       CONCAT(TRIM(TRAILING '.0' FROM m3_pct), '%')     AS M3
FROM pivoted
ORDER BY cohort_month;
```

## 🪜 Step-by-Step Explanation

**Step 1: find each user's cohort (`first_activity`)**
- `MIN(activity_date)` with `GROUP BY user_id` gives each user's **first** active day.
- `DATE_FORMAT(date, '%Y-%m-01')` rewrites a date as the 1st of its month: `2025-01-07` becomes `'2025-01-01'`. `%Y` = 4-digit year, `%m` = 2-digit month, and `01` is written as-is.
- `CAST(… AS DATE)` turns that text back into a real `DATE`.
- MySQL has no `DATE_TRUNC('month', …)` (that's PostgreSQL), so this pair of functions does the same job.

**Step 2: list every month each user was active (`user_months`)**
The same month conversion, with `SELECT DISTINCT`. A user active on 5 days in February gives just **one** February row.

**Step 3: how many months after joining? (`cohort_activity`)**
- `YEAR(d) * 12 + MONTH(d)` turns a month into one running count of months, so two months can be subtracted.
- Activity month − cohort month = **month_number**: 0 = the joining month, 1 = one month later, and so on.
- Example: user 6 joined Feb (2025 × 12 + 2) and was active in Apr (2025 × 12 + 4), so month_number = 2.

**Step 4: count users per cohort per month (`retention_long`)**
`GROUP BY cohort_month, month_number` with `COUNT(DISTINCT user_id)`:

```text
+--------------+--------------+----------------+
| cohort_month | month_number | retained_users |
+--------------+--------------+----------------+
| 2025-01-01   |            0 |              5 |
| 2025-01-01   |            1 |              4 |
| 2025-01-01   |            2 |              3 |
| 2025-01-01   |            3 |              2 |
| 2025-02-01   |            0 |              4 |
| 2025-02-01   |            1 |              2 |
| 2025-02-01   |            2 |              1 |
| 2025-03-01   |            0 |              3 |
| 2025-03-01   |            1 |              1 |
| 2025-04-01   |            0 |              4 |
+--------------+--------------+----------------+
```

This is the "long" format: one row per cohort per month.

**Step 5: cohort size (`cohort_size`)**
The `month_number = 0` row is the number of users who joined, M0. It's the denominator for every percentage of that cohort.

**Step 6: pivot to columns and convert to % (`pivoted`)**
- `GROUP BY r.cohort_month, s.total_users` gives one row per cohort.
- `MAX(CASE WHEN r.month_number = 1 THEN r.retained_users END)` picks the month-1 count into its own column (NULL on other rows; `MAX` ignores NULLs). The same is done for 0, 2 and 3.
- `100.0 * count / s.total_users` turns it into a percentage, and `ROUND(…, 1)` keeps 1 decimal. Jan M1 = 100 × 4 / 5 = 80.0.
- **The triangle:** the Feb cohort has no month-3 row (that would be May, which isn't in the data). `MAX(CASE …)` returns NULL, and NULL ÷ anything = NULL, so the cell stays empty.

**Step 7: add the % sign**
- `TRIM(TRAILING '.0' FROM m1_pct)` removes a `.0` at the end of the number, so `80.0` becomes `80`. A value like `33.3` doesn't end in `.0`, so it's left as it is.
- `CONCAT(text, '%')` joins two pieces of text: `'80'` + `'%'` gives **`'80%'`**.
- If the value is NULL, `CONCAT(NULL, '%')` returns **NULL** (any `CONCAT` with a NULL piece is NULL), so the empty cells of the triangle stay NULL instead of becoming a bare `'%'`.
- `DATE_FORMAT(cohort_month, '%Y-%m')` displays the cohort as `2025-01`.
- The percentage is calculated in Step 6 and formatted in Step 7. Keeping these two steps separate keeps each line short and easy to read.

> **Note:** after Step 7, M1–M3 are **text**, not numbers. That's fine for a report, but if you need to sort or do maths on them, use the numeric `m1_pct` … `m3_pct` columns from Step 6.

> **Edge case:** if a month *inside* the data had zero returning users, it would also show NULL instead of 0, because no row exists for it. To show 0 there, wrap the expression in `COALESCE(…, 0)` for months that fall within the data range.

---

[⬅ Back to the question](Question.md) &nbsp;|&nbsp; [🏠 All questions](../README.md)
