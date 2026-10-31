# 💡 11) Play Where the Total Score First Turns Negative: Solution

**Difficulty:** 🟡 Medium &nbsp;|&nbsp; **Concepts used:** Running total with SUM() OVER (PARTITION BY … ORDER BY …), CTEs, ROW_NUMBER() to pick the first row

> [📄 Question](Question.md) &nbsp;|&nbsp; 💡 **Solution** &nbsp;|&nbsp; [🏠 All questions](../README.md)

## 🧾 Solution Code (MySQL 8.0+)

```sql
-- Step 1: running total score of each user, in time order
WITH running AS (
    SELECT user_id,
           play_id,
           play_timestamp,
           score,
           SUM(score) OVER (PARTITION BY user_id
                            ORDER BY play_timestamp) AS running_score
    FROM plays
),

-- Step 2: keep the negative moments and number them in time order
negative_plays AS (
    SELECT user_id,
           play_id,
           play_timestamp,
           score,
           ROW_NUMBER() OVER (PARTITION BY user_id
                              ORDER BY play_timestamp) AS rn
    FROM running
    WHERE running_score < 0
)

-- Step 3: the first negative moment per user
SELECT user_id,
       play_id,
       play_timestamp,
       score
FROM negative_plays
WHERE rn = 1
ORDER BY user_id;
```

## 🪜 Step-by-Step Explanation

**Step 1: calculate the running total (`SUM() OVER`)**

```sql
SUM(score) OVER (PARTITION BY user_id ORDER BY play_timestamp) AS running_score
```
- Normally `SUM` collapses rows into one total. With `OVER (…)` it becomes a **window function**: every row keeps its place and gets the sum *up to and including itself*.
- `PARTITION BY user_id` gives each user their own running total, starting from zero.
- `ORDER BY play_timestamp` sets the order in which scores are added.

```text
+---------+---------+---------------------+-------+---------------+
| user_id | play_id | play_timestamp      | score | running_score |
+---------+---------+---------------------+-------+---------------+
|       1 |    1234 | 2025-01-01 16:00:00 |   100 |           100 |
|       1 |    5678 | 2025-01-01 16:05:00 |   150 |           250 |
|       1 |    4235 | 2025-01-01 16:10:00 |  -100 |           150 |
|       1 |    3456 | 2025-01-01 16:12:00 |  -250 |          -100 |
|       1 |    8654 | 2025-01-01 16:15:00 |   400 |           300 |
|       2 |    6767 | 2025-01-01 17:00:00 |   100 |           100 |
|       2 |    3476 | 2025-01-01 17:05:00 |   200 |           300 |
|       2 |    6665 | 2025-01-01 17:10:00 |  -150 |           150 |
|       2 |    5346 | 2025-01-01 17:15:00 |   -50 |           100 |
|       2 |    4356 | 2025-01-01 17:20:00 |    75 |           175 |
|       2 |    4366 | 2025-01-01 17:25:00 |   -75 |           100 |
+---------+---------+---------------------+-------+---------------+
```

- User 1's total goes 100 → 250 → 150 → **−100** at play 3456, then back to 300.
- User 2's total never drops below 0, so user 2 must not appear.
- Play 4235 has a negative *score* (−100) but the *total* is still +150, so it's not the answer.

**Step 2: keep only negative totals and number them (`WHERE` + `ROW_NUMBER`)**
- `WHERE running_score < 0` must be in a separate step (a new CTE). A window function's result can't be filtered in the same query where it's calculated, because `WHERE` runs before `SELECT`.
- If a total went negative several times, we only want the **first**. `ROW_NUMBER() OVER (PARTITION BY user_id ORDER BY play_timestamp)` numbers each user's negative plays 1, 2, 3 … in time order.

**Step 3: `WHERE rn = 1`** keeps the earliest negative play per user: user 1, play 3456, at 16:12, with a score of −250.

> **Note:** when the window has an `ORDER BY`, plays with the *same* timestamp are added together in one step. Here every timestamp is unique, so this doesn't matter.

---

[⬅ Back to the question](Question.md) &nbsp;|&nbsp; [🏠 All questions](../README.md)
