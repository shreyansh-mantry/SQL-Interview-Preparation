# 💡 13) Longest Ride Streak: Solution

**Difficulty:** 🔴 Hard &nbsp;|&nbsp; **Concepts used:** Gaps and islands, SELECT DISTINCT, ROW_NUMBER(), DATE_SUB() with INTERVAL, GROUP BY, COUNT(), MAX()

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

-- Step 3: one row per streak, with its length
streaks AS (
    SELECT user_id,
           grp,
           COUNT(*) AS streak_length
    FROM grouped
    GROUP BY user_id, grp
)

-- Step 4: the longest streak per user
SELECT user_id,
       MAX(streak_length) AS max_streak
FROM streaks
GROUP BY user_id
ORDER BY user_id;
```

## 🪜 Step-by-Step Explanation

This is the classic **"gaps and islands"** problem: finding runs (islands) of consecutive values separated by gaps.

**Step 1: one row per user per day (`ride_days`)**
`SELECT DISTINCT user_id, ride_date` removes repeats. User B took two rides on 22 Feb (Y4 and Y5). Without `DISTINCT`, that day would be counted twice and make the streak look one day longer than it is.

**Step 2: the date-minus-row-number trick (`grouped`)**
- `ROW_NUMBER() OVER (PARTITION BY user_id ORDER BY ride_date)` numbers each user's ride days 1, 2, 3 … in date order. `PARTITION BY user_id` restarts the numbering for each user.
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
| B       | 2020-02-18 |  1 | 2020-02-17 |
| B       | 2020-02-19 |  2 | 2020-02-17 |
| B       | 2020-02-20 |  3 | 2020-02-17 |
| B       | 2020-02-21 |  4 | 2020-02-17 |
| B       | 2020-02-22 |  5 | 2020-02-17 |
| B       | 2020-02-26 |  6 | 2020-02-20 |
| B       | 2020-02-27 |  7 | 2020-02-20 |
+---------+------------+----+------------+
```

Rows with the same `grp` belong to the same streak. `grp` is just a label; the date itself has no meaning.

**Step 3: measure each streak (`streaks`)**
`GROUP BY user_id, grp` puts each streak's days in one bucket, and `COUNT(*)` counts the days in it:

```text
+---------+------------+--------------+------------+---------------+
| user_id | grp        | streak_start | streak_end | streak_length |
+---------+------------+--------------+------------+---------------+
| A       | 2020-02-18 | 2020-02-19   | 2020-02-21 |             3 |
| A       | 2020-02-20 | 2020-02-24   | 2020-02-27 |             4 |
| B       | 2020-02-17 | 2020-02-18   | 2020-02-22 |             5 |
| B       | 2020-02-20 | 2020-02-26   | 2020-02-27 |             2 |
+---------+------------+--------------+------------+---------------+
```

(`streak_start` and `streak_end` are shown here only to make each streak easy to see. The solution doesn't need them.)

**Step 4: keep the longest streak (`MAX`)**
`GROUP BY user_id` with `MAX(streak_length)` picks each user's longest streak:
- User A: streaks of 3 and 4 days, so the max is **4**.
- User B: streaks of 5 and 2 days, so the max is **5**. B's *latest* streak is only 2 days. That's the difference between a **max** streak and a **current** streak.

> **Variation, current streak:** to get the streak that ends on the user's latest ride instead, also keep `MAX(ride_date) AS streak_end` in Step 3. Then join to each user's `MAX(ride_date)` from `rides` and keep the streak whose `streak_end` matches. For this data that gives A = 4, B = 2.

---

[⬅ Back to the question](Question.md) &nbsp;|&nbsp; [🏠 All questions](../README.md)
