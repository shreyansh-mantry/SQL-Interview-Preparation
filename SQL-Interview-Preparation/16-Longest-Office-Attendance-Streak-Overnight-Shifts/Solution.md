# 💡 16) Longest Office Attendance Streak (Overnight Shifts): Solution

**Difficulty:** 🔴 Hard &nbsp;|&nbsp; **Concepts used:** Recursive CTE (WITH RECURSIVE), DATE(), INTERVAL, gaps and islands, ROW_NUMBER(), MIN() / MAX()

> [📄 Question](Question.md) &nbsp;|&nbsp; 💡 **Solution** &nbsp;|&nbsp; [🏠 All questions](../README.md)

## 🧾 Solution Code (MySQL 8.0+)

```sql
-- Step 1: expand every session into each calendar day it touches
WITH RECURSIVE session_days AS (
    SELECT employee_id,
           login_time,
           logout_time,
           DATE(login_time) AS present_date              -- first day of the session
    FROM employee_attendance

    UNION ALL

    SELECT employee_id,
           login_time,
           logout_time,
           present_date + INTERVAL 1 DAY                 -- next day ...
    FROM session_days
    WHERE present_date < DATE(logout_time)               -- ... until the logout day
),

-- Step 2: one row per employee per present day
present_days AS (
    SELECT employee_id,
           present_date,
           MIN(login_time)  AS first_login,
           MAX(logout_time) AS last_logout
    FROM session_days
    GROUP BY employee_id, present_date
),

-- Step 3: date minus row number = the same value for consecutive days
grouped AS (
    SELECT employee_id,
           present_date,
           first_login,
           last_logout,
           DATE_SUB(present_date,
                    INTERVAL ROW_NUMBER() OVER (PARTITION BY employee_id ORDER BY present_date) DAY) AS grp
    FROM present_days
),

-- Step 4: one row per streak
streaks AS (
    SELECT employee_id,
           MIN(first_login) AS streak_login_time,
           MAX(last_logout) AS streak_logout_time,
           COUNT(*)         AS streak_days
    FROM grouped
    GROUP BY employee_id, grp
),

-- Step 5: rank streaks, longest first
ranked AS (
    SELECT employee_id,
           streak_login_time,
           streak_logout_time,
           streak_days,
           ROW_NUMBER() OVER (PARTITION BY employee_id
                              ORDER BY streak_days DESC, streak_login_time) AS rn
    FROM streaks
)

SELECT employee_id,
       streak_login_time  AS login_time,
       streak_logout_time AS logout_time,
       streak_days        AS max_streak_days
FROM ranked
WHERE rn = 1
ORDER BY employee_id;
```

## 🪜 Step-by-Step Explanation

The hard part is the overnight session (2 Jan 10 pm → 3 Jan 6 am). There's no row that *starts* on 3 Jan, yet the employee was in office on 3 Jan. So first we turn each session into one row per day it touches.

**Step 1: expand sessions into days (`WITH RECURSIVE session_days`)**

A **recursive CTE** is a query that keeps feeding its own output back into itself until a stop condition is met. It has two parts joined by `UNION ALL`:
- **Anchor (first part):** runs once. `DATE(login_time)` cuts the time off a datetime (`2025-01-02 22:00:00` becomes `2025-01-02`), giving each session's first day.
- **Recursive part (second part):** reads the rows produced so far (`FROM session_days`) and adds one more day: `present_date + INTERVAL 1 DAY`.
- **Stop condition:** `WHERE present_date < DATE(logout_time)`. Days keep being added only while we haven't reached the logout day. Normal 8 am–4 pm sessions stop right away; the overnight session gets one extra row for 3 Jan.

```text
+-------------+---------------------+---------------------+--------------+
| employee_id | login_time          | logout_time         | present_date |
+-------------+---------------------+---------------------+--------------+
|           1 | 2025-01-01 08:00:00 | 2025-01-01 16:00:00 | 2025-01-01   |
|           1 | 2025-01-02 08:00:00 | 2025-01-02 16:00:00 | 2025-01-02   |
|           1 | 2025-01-02 22:00:00 | 2025-01-03 06:00:00 | 2025-01-02   |
|           1 | 2025-01-02 22:00:00 | 2025-01-03 06:00:00 | 2025-01-03   |
|           1 | 2025-01-04 08:00:00 | 2025-01-04 16:00:00 | 2025-01-04   |
|           1 | 2025-01-06 08:00:00 | 2025-01-06 16:00:00 | 2025-01-06   |
|           1 | 2025-01-07 08:00:00 | 2025-01-07 16:00:00 | 2025-01-07   |
+-------------+---------------------+---------------------+--------------+
```

**Step 2: one row per present day (`present_days`)**
2 Jan now has two rows (the day shift and the night shift). `GROUP BY employee_id, present_date` merges them into one day. `MIN(login_time)` and `MAX(logout_time)` keep that day's earliest login and latest logout, which we'll need for the output.

Present days: 1, 2, 3, 4, 6, 7 Jan. 5 Jan is missing, which means leave.

**Step 3: label the consecutive runs (`grouped`)**
This is the same *gaps and islands* trick as the ride-streak question:
- `ROW_NUMBER() OVER (PARTITION BY employee_id ORDER BY present_date)` numbers the days 1, 2, 3 …
- `DATE_SUB(present_date, INTERVAL row_number DAY)` gives the **same date for consecutive days**. 1–4 Jan all give 31 Dec; 6–7 Jan both give 1 Jan.

**Step 4: summarise each streak (`streaks`)**
`GROUP BY employee_id, grp`:
- `COUNT(*)` gives the streak length: 4 days and 2 days.
- `MIN(first_login)` gives the first login of the streak: 1 Jan 08:00.
- `MAX(last_logout)` gives the last logout of the streak: 4 Jan 16:00.

**Step 5: keep the longest streak (`ranked`)**
`ROW_NUMBER() OVER (PARTITION BY employee_id ORDER BY streak_days DESC, streak_login_time)` numbers each employee's streaks from longest to shortest. If two streaks are equally long, the earlier one comes first. `WHERE rn = 1` keeps the longest: **1 Jan 08:00 → 4 Jan 16:00, 4 days**.

> **Edge case:** a logout at exactly `00:00:00` would count the employee as present on that new day. If that shouldn't count, use `DATE(logout_time - INTERVAL 1 SECOND)` in the stop condition.

---

[⬅ Back to the question](Question.md) &nbsp;|&nbsp; [🏠 All questions](../README.md)
