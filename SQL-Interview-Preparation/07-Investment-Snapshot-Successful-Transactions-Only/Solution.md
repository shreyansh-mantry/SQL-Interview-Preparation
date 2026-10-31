# 💡 7) Investment Snapshot (Successful Transactions Only): Solution

**Difficulty:** 🟡 Medium &nbsp;|&nbsp; **Concepts used:** LEFT JOIN, CASE WHEN, conditional aggregation with SUM() and MAX(), GROUP BY

> [📄 Question](Question.md) &nbsp;|&nbsp; 💡 **Solution** &nbsp;|&nbsp; [🏠 All questions](../README.md)

## 🧾 Solution Code (MySQL 8.0+)

```sql
SELECT
    o.user_id,
    MAX(CASE WHEN o.order_kind = 'First Order'  AND t.status = 'Success' THEN 1 ELSE 0 END) AS first_order_success_flag,
    SUM(CASE WHEN o.order_kind = 'Repeat Order' AND t.status = 'Success' THEN 1 ELSE 0 END) AS repeat_order_success_count,
    SUM(CASE WHEN t.status = 'Success' THEN t.amount ELSE 0 END)                            AS total_amount_invested
FROM orders o
LEFT JOIN transactions t
       ON o.txn_id = t.txn_id
GROUP BY o.user_id
ORDER BY o.user_id;
```

## 🪜 Step-by-Step Explanation

**Step 1: bring each order's transaction details next to it (`LEFT JOIN`)**
- `orders o LEFT JOIN transactions t ON o.txn_id = t.txn_id` puts the status and amount beside every order.
- `LEFT JOIN` keeps **every order**, even if a transaction were missing, so every user is guaranteed to stay in the result.

```text
+---------+--------+--------------+----------+--------+
| user_id | txn_id | order_kind   | status   | amount |
+---------+--------+--------------+----------+--------+
| A       | Y12    | First Order  | Success  |    500 |
| A       | Y15    | Repeat Order | Success  |    250 |
| B       | Y13    | First Order  | Rejected |   5000 |
| B       | Y17    | Repeat Order | Success  |    200 |
| B       | Y18    | Repeat Order | Success  |   2500 |
| C       | Y16    | First Order  | Rejected |    700 |
| D       | Y14    | First Order  | Success  |    100 |
| D       | Y19    | Repeat Order | Success  |    500 |
| D       | Y20    | Repeat Order | Success  |   2000 |
| D       | Y21    | Repeat Order | Success  |    200 |
| D       | Y22    | Repeat Order | Rejected |    400 |
+---------+--------+--------------+----------+--------+
```

**Step 2: group the rows per user (`GROUP BY o.user_id`)**: each user's orders go into one bucket, so we get one output row per user.

**Step 3: calculate each column with "conditional aggregation"**

`CASE WHEN condition THEN x ELSE y END` is SQL's *if / else*. It checks each row and returns `x` if the condition is true, otherwise `y`. Putting a `CASE` **inside** an aggregate lets us count or add up only the rows we care about:

- **`first_order_success_flag`**: `CASE` returns 1 for a successful *First Order* row and 0 for any other row. `MAX(…)` takes the largest value in the bucket, so the result is **1** if any row is 1, else **0**.
  A → Y12 Success → 1. B → Y13 Rejected → 0. C → Y16 Rejected → 0. D → Y14 Success → 1.
- **`repeat_order_success_count`**: `CASE` returns 1 for each successful *Repeat Order* row and 0 for the rest. `SUM(…)` adds the 1s, which counts them.
  A → 1 (Y15). B → 2 (Y17, Y18). C → 0. D → 3 (Y19, Y20, Y21; Y22 was rejected).
- **`total_amount_invested`**: `CASE` returns the amount for successful rows and 0 for the rest. `SUM(…)` adds them up.
  A → 500 + 250 = 750. B → 200 + 2500 = 2700. C → 0. D → 100 + 500 + 2000 + 200 = 2800.

**Why not just `WHERE t.status = 'Success'`?**
`WHERE` would delete user C's only row *before* grouping, so C would disappear from the output entirely. The question says every user must appear. Putting the condition inside `CASE` keeps all rows and only decides what each row *contributes*.

---

[⬅ Back to the question](Question.md) &nbsp;|&nbsp; [🏠 All questions](../README.md)
