# 💡 14) Peak Concurrent Active Policies: Solution

**Difficulty:** 🔴 Hard &nbsp;|&nbsp; **Concepts used:** Sweep-line / event counting, UNION ALL, DATE_ADD(), GROUP BY with SUM(), running total with SUM() OVER, scalar subquery with MAX()

> [📄 Question](Question.md) &nbsp;|&nbsp; 💡 **Solution** &nbsp;|&nbsp; [🏠 All questions](../README.md)

## 🧾 Solution Code (MySQL 8.0+)

```sql
-- Step 1: +1 when a policy starts, -1 on the day after it ends
WITH events AS (
    SELECT policy_start_date AS event_date, 1 AS delta
    FROM policies
    UNION ALL
    SELECT DATE_ADD(policy_end_date, INTERVAL 1 DAY) AS event_date, -1 AS delta
    FROM policies
),

-- Step 2: net change per date
daily_net AS (
    SELECT event_date,
           SUM(delta) AS net_change
    FROM events
    GROUP BY event_date
),

-- Step 3: running total = number of active policies from that date
running AS (
    SELECT event_date,
           SUM(net_change) OVER (ORDER BY event_date) AS active_policies
    FROM daily_net
)

-- Step 4: the date(s) with the highest count
SELECT event_date      AS peak_date,
       active_policies AS max_concurrent_policies
FROM running
WHERE active_policies = (SELECT MAX(active_policies) FROM running)
ORDER BY event_date;
```

## 🪜 Step-by-Step Explanation

Checking every day against every policy would be slow. Instead we only look at the days when the count **changes**. This is called the *sweep line* (or event) method.

**Step 1: turn each policy into two events (`events`)**
- On its **start date**, a policy adds **+1** to the active count.
- On the **day after its end date**, it removes **−1**. The end date is inclusive, so the policy is still active *on* its end date; it stops counting the next day.
- `DATE_ADD(policy_end_date, INTERVAL 1 DAY)` adds one day to a date.
- `UNION ALL` stacks the "start" rows and the "end" rows into one list (7 policies → 14 events).

**Step 2: net change per date (`daily_net`)**
Several events can fall on the same date (two policies start on 5 Jan). `GROUP BY event_date` with `SUM(delta)` combines them into one net change per date.

**Step 3: running total (`running`)**
`SUM(net_change) OVER (ORDER BY event_date)` adds up all the changes so far, in date order. The running total on a date is the number of policies active **from that date until the next change date**.

```text
+------------+------------+-----------------+
| event_date | net_change | active_policies |
+------------+------------+-----------------+
| 2025-01-01 |          1 |               1 |
| 2025-01-03 |          1 |               2 |
| 2025-01-05 |          2 |               4 |
| 2025-01-07 |         -1 |               3 |
| 2025-01-09 |          0 |               3 |
| 2025-01-11 |          0 |               3 |
| 2025-01-13 |         -1 |               2 |
| 2025-01-14 |          1 |               3 |
| 2025-01-16 |         -1 |               2 |
| 2025-01-19 |         -1 |               1 |
| 2025-01-21 |         -1 |               0 |
+------------+------------+-----------------+
```

**Step 4: pick the peak**
- `(SELECT MAX(active_policies) FROM running)` is a *scalar subquery*: it returns one single value, 4.
- `WHERE active_policies = …` keeps the date(s) with that value: **5 Jan, with 4 policies** (101, 102, 103, 104).

> **Note:** the answer is the date the peak *starts*. The count stays at 4 until the next change date (7 Jan, when policy 102 stops counting), so 4 policies are active on both 5 and 6 Jan.

---

[⬅ Back to the question](Question.md) &nbsp;|&nbsp; [🏠 All questions](../README.md)
