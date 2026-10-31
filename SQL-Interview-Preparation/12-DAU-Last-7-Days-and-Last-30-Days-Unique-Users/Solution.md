# 💡 12) DAU, Last 7 Days and Last 30 Days Unique Users: Solution

**Difficulty:** 🟡 Medium &nbsp;|&nbsp; **Concepts used:** SELECT DISTINCT, date arithmetic with INTERVAL, range join with BETWEEN, COUNT(DISTINCT CASE …)

> [📄 Question](Question.md) &nbsp;|&nbsp; 💡 **Solution** &nbsp;|&nbsp; [🏠 All questions](../README.md)

## 🧾 Solution Code (MySQL 8.0+)

```sql
-- Step 1: the list of dates to report on
WITH report_dates AS (
    SELECT DISTINCT login_date AS activity_date
    FROM user_logins
)

-- Step 2 + 3: attach the last 30 days of logins to each date, then count
SELECT d.activity_date,
       COUNT(DISTINCT CASE WHEN l.login_date = d.activity_date
                           THEN l.user_id END)                         AS dau,
       COUNT(DISTINCT CASE WHEN l.login_date >= d.activity_date - INTERVAL 6 DAY
                           THEN l.user_id END)                         AS last_7_days_users,
       COUNT(DISTINCT l.user_id)                                       AS last_30_days_users
FROM report_dates d
JOIN user_logins l
  ON l.login_date BETWEEN d.activity_date - INTERVAL 29 DAY
                      AND d.activity_date
GROUP BY d.activity_date
ORDER BY d.activity_date;
```

## 🪜 Step-by-Step Explanation

**Step 1: list the dates to report on (`report_dates`)**
`SELECT DISTINCT login_date` returns each login date once: 1, 2, 3, 10, 20, 25 and 31 Jan.

**Step 2: attach each date's last 30 days of logins (range join)**

```sql
JOIN user_logins l
  ON l.login_date BETWEEN d.activity_date - INTERVAL 29 DAY AND d.activity_date
```
- `d.activity_date - INTERVAL 29 DAY` subtracts 29 days from a date. Counting the day itself, that covers **30 days**.
- `BETWEEN a AND b` is true when the value is from `a` up to `b`, including both ends.
- So each report date is joined to every login in its 30-day window. One login can be attached to several report dates. That's expected, because it belongs to several windows.

**Step 3: count unique users in three window sizes**
- `GROUP BY d.activity_date` gives one bucket per report date, holding its 30 days of logins.
- **30 days:** `COUNT(DISTINCT l.user_id)` counts unique users in the whole bucket.
- **7 days:** `CASE WHEN l.login_date >= d.activity_date - INTERVAL 6 DAY THEN l.user_id END` returns the user only for logins in the last 7 days (6 days back + today), and NULL otherwise. `COUNT(DISTINCT …)` ignores NULLs, so only those users are counted.
- **DAU:** same idea, with `l.login_date = d.activity_date`, meaning only that exact day.

**Worked example: 25 Jan**

| Window | Dates covered | Users | Count |
|---|---|---|---|
| DAU | 25 Jan | u1, u2 | 2 |
| 7 days | 19 Jan – 25 Jan | u5 (20th), u1, u2 | 3 |
| 30 days | 27 Dec – 25 Jan | u1, u2, u3, u4, u5 | 5 |

> **Why not a window function?** MySQL doesn't allow `COUNT(DISTINCT …) OVER (…)`, so a rolling *distinct* count has to be built with a join like this one.

---

[⬅ Back to the question](Question.md) &nbsp;|&nbsp; [🏠 All questions](../README.md)
