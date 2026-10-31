# 💡 13) Current Ride Streak: Solution

**Difficulty:** 🔴 Hard &nbsp;|&nbsp; **Concepts used:** Gaps and islands, SELECT DISTINCT, ROW_NUMBER(), DATE_SUB() with INTERVAL, GROUP BY, MAX(), joining to a derived table

> [📄 Question](Question.md) &nbsp;|&nbsp; 💡 **Solution** &nbsp;|&nbsp; [🏠 All questions](../README.md)

## 🧾 Solution Code (MySQL 8.0+)

```sql
-- Step 1: one row per user per day ridden
WITH ride_days AS (
    SELECT DISTINCT user_id, ride_date
    FROM rides
),

-- Step 2: date minus row number = the same value for consecutive days
grouped AS (
    SELECT user_id,
           ride_date,
           DATE_SUB(ride_date,
                    INTERVAL ROW_NUMBER() OVER (PARTITION BY user_id ORDER BY ride_date) DAY) AS grp
    FROM ride_days
),

-- Step 3: one row per streak
streaks AS (
    SELECT user_id,
           grp,
           COUNT(*)       AS streak_length,
           MAX(ride_date) AS streak_end
    FROM grouped
    GROUP BY user_id, grp
)

-- Step 4: the streak that ends on the user's latest ride
SELECT s.user_id,
       s.streak_length AS current_streak
FROM streaks s
JOIN (SELECT user_id, MAX(ride_date) AS last_ride
      FROM rides
      GROUP BY user_id) lr
  ON  s.user_id    = lr.user_id
  AND s.streak_end = lr.last_ride
ORDER BY s.user_id;
```

## 🪜 Step-by-Step Explanation

This is the classic **"gaps and islands"** problem: finding runs (islands) of consecutive values separated by gaps.

**Step 1: one row per user per day (`ride_days`)**
`SELECT DISTINCT user_id, ride_date` removes repeats. If a user took two rides on the same day, that day must still count only once.

**Step 2: the date-minus-row-number trick (`grouped`)**
- `ROW_NUMBER() OVER (PARTITION BY user_id ORDER BY ride_date)` numbers each user's ride days 1, 2, 3 … in date order.
- `DATE_SUB(date, INTERVAL n DAY)` subtracts *n* days from a date.
- When days are consecutive, the date goes up by 1 and the row number also goes up by 1, so **date − row number stays the same**. When there's a gap, the date jumps but the row number doesn't, so the result changes.

```text
+---------+------------+----+------------+
| user_id | ride_date  | rn | grp        |
+---------+------------+----+------------+
| A       | 2020-02-19 |  1 | 2020-02-18 |
| A       | 2020-02-20 |  2 | 2020-02-18 |
| A       | 2020-02-21 |  3 | 2020-02-18 |
| A       | 2020-02-24 |  4 | 2020-02-20 |
| A       | 2020-02-25 |  5 | 2020-02-20 |
| A       | 2020-02-26 |  6 | 2020-02-20 |
| A       | 2020-02-27 |  7 | 2020-02-20 |
+---------+------------+----+------------+
```

19–21 Feb all give `2020-02-18`, which is one streak. 24–27 Feb all give `2020-02-20`, which is a second streak. `grp` is just a label; the date itself has no meaning.

**Step 3: summarise each streak (`streaks`)**
- `GROUP BY user_id, grp` puts each streak's days in one bucket.
- `COUNT(*)` is the streak length (3 and 4). `MAX(ride_date)` is the day the streak ended (21 Feb and 27 Feb).

**Step 4: pick the current streak**
- The derived table `(SELECT user_id, MAX(ride_date) AS last_ride … GROUP BY user_id)` finds each user's latest ride: 27 Feb.
- Joining on `s.streak_end = lr.last_ride` keeps only the streak that ends on that day, so the current streak is **4**.

---

[⬅ Back to the question](Question.md) &nbsp;|&nbsp; [🏠 All questions](../README.md)
