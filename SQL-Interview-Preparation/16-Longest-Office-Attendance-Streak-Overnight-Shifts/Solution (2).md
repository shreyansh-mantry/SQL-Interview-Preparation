# 💡 16) Longest Office Attendance Streak (Overnight Shifts): Solution

**Difficulty:** 🔴 Hard &nbsp;|&nbsp; **Concepts used:** DATE(), UNION vs UNION ALL, gaps and islands, ROW_NUMBER(), DATE_SUB() with INTERVAL, MIN() / MAX() / COUNT()

> [📄 Question](Question.md) &nbsp;|&nbsp; 💡 **Solution** &nbsp;|&nbsp; [🏠 All questions](../README.md)

## 🧾 Solution Code (MySQL 8.0+)

```sql
-- Step 1: convert the timestamps to dates (day-level, no time)
WITH session_dates AS (
    SELECT employee_id,
           DATE(login_time)  AS login_date,
           DATE(logout_time) AS logout_date
    FROM employee_attendance
),

-- Step 2: UNION the login dates and logout dates = one row per day present
present_days AS (
    SELECT employee_id, login_date  AS present_date FROM session_dates
    UNION
    SELECT employee_id, logout_date AS present_date FROM session_dates
),

-- Step 3: streak calculation, date minus row number = same value for consecutive days
grouped AS (
    SELECT employee_id,
           present_date,
           DATE_SUB(present_date,
                    INTERVAL ROW_NUMBER() OVER (PARTITION BY employee_id ORDER BY present_date) DAY) AS grp
    FROM present_days
),

-- Step 4: one row per streak, with its first day, last day and length
streaks AS (
    SELECT employee_id,
           MIN(present_date) AS streak_start,
           MAX(present_date) AS streak_end,
           COUNT(*)          AS streak_days
    FROM grouped
    GROUP BY employee_id, grp
),

-- Step 5: rank each employee's streaks, longest first
ranked AS (
    SELECT employee_id,
           streak_start,
           streak_end,
           streak_days,
           ROW_NUMBER() OVER (PARTITION BY employee_id
                              ORDER BY streak_days DESC, streak_start) AS rn
    FROM streaks
)

SELECT employee_id,
       streak_start AS login_date,
       streak_end   AS logout_date,
       streak_days  AS max_streak_days
FROM ranked
WHERE rn = 1
ORDER BY employee_id;
```

## 🪜 Step-by-Step Explanation

The solution works at **day level**: first turn every timestamp into a date, then collect the days present, then calculate the streaks.

**Step 1: convert timestamps to dates (`session_dates`)**
- `DATE(login_time)` cuts the time off a datetime: `2025-01-02 22:00:00` becomes `2025-01-02`. The same is done for `logout_time`.
- After this, the time of day no longer matters. Only *which day* the employee logged in or out.

```text
+-------------+------------+-------------+
| employee_id | login_date | logout_date |
+-------------+------------+-------------+
|           1 | 2025-01-01 | 2025-01-01  |
|           1 | 2025-01-02 | 2025-01-02  |
|           1 | 2025-01-02 | 2025-01-03  |
|           1 | 2025-01-04 | 2025-01-04  |
|           1 | 2025-01-06 | 2025-01-06  |
|           1 | 2025-01-07 | 2025-01-07  |
+-------------+------------+-------------+
```

The overnight session (2 Jan 10 pm → 3 Jan 6 am) now shows `login_date = 2025-01-02` and `logout_date = 2025-01-03`. That's how 3 Jan gets counted as a day present, even though no session *starts* on 3 Jan.

**Step 2: one list of days present (`UNION`)**
- The first `SELECT` lists every login date, and the second lists every logout date.
- `UNION` stacks the two lists **and removes duplicates**. A normal 8 am–4 pm session has the same login and logout date, and 2 Jan appears in several rows, so without de-duplication those days would be counted more than once.
- `UNION ALL` would keep the duplicates, which is wrong here; that's why plain `UNION` is used.
- A session never lasts more than 24 hours, so it can only touch its login day and its logout day. Listing these two dates covers every day present.

```text
+-------------+--------------+
| employee_id | present_date |
+-------------+--------------+
|           1 | 2025-01-01   |
|           1 | 2025-01-02   |
|           1 | 2025-01-03   |
|           1 | 2025-01-04   |
|           1 | 2025-01-06   |
|           1 | 2025-01-07   |
+-------------+--------------+
```

Days present: 1, 2, 3, 4, 6, 7 Jan. 5 Jan is missing, which means leave.

**Step 3: label the consecutive runs (`grouped`)**
This is the *gaps and islands* trick:
- `ROW_NUMBER() OVER (PARTITION BY employee_id ORDER BY present_date)` numbers each employee's days 1, 2, 3 … in date order.
- `DATE_SUB(present_date, INTERVAL row_number DAY)` subtracts that number of days from the date.
- When days are consecutive, the date and the row number both go up by 1, so the result stays the **same**. After a gap, the date jumps but the row number doesn't, so the result changes.

```text
+-------------+--------------+----+------------+
| employee_id | present_date | rn | grp        |
+-------------+--------------+----+------------+
|           1 | 2025-01-01   |  1 | 2024-12-31 |
|           1 | 2025-01-02   |  2 | 2024-12-31 |
|           1 | 2025-01-03   |  3 | 2024-12-31 |
|           1 | 2025-01-04   |  4 | 2024-12-31 |
|           1 | 2025-01-06   |  5 | 2025-01-01 |
|           1 | 2025-01-07   |  6 | 2025-01-01 |
+-------------+--------------+----+------------+
```

1–4 Jan all get `grp = 2024-12-31`, which is one streak. 6–7 Jan get `grp = 2025-01-01`, which is a second streak. `grp` is just a label.

**Step 4: summarise each streak (`streaks`)**
`GROUP BY employee_id, grp` puts each streak's days in one bucket:
- `MIN(present_date)` gives the first day of the streak.
- `MAX(present_date)` gives the last day of the streak.
- `COUNT(*)` gives the number of days.

```text
+-------------+--------------+------------+-------------+
| employee_id | streak_start | streak_end | streak_days |
+-------------+--------------+------------+-------------+
|           1 | 2025-01-01   | 2025-01-04 |           4 |
|           1 | 2025-01-06   | 2025-01-07 |           2 |
+-------------+--------------+------------+-------------+
```

**Step 5: keep the longest streak (`ranked`)**
- `ROW_NUMBER() OVER (PARTITION BY employee_id ORDER BY streak_days DESC, streak_start)` numbers each employee's streaks from longest to shortest. If two streaks are equally long, the earlier one comes first.
- `WHERE rn = 1` keeps the longest: **1 Jan → 4 Jan, 4 days**.
- The final `SELECT` renames the columns to `login_date`, `logout_date` and `max_streak_days`, as the question asks.

> **If sessions could last more than 24 hours**, a 3-day session (login 1 Jan, logout 3 Jan) would miss 2 Jan, because Step 2 only lists the login and logout days. In that case, generate every day between `login_date` and `logout_date` (for example with a recursive CTE) instead of using the `UNION`.

---

[⬅ Back to the question](Question.md) &nbsp;|&nbsp; [🏠 All questions](../README.md)
