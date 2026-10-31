# 💡 8) KYC: Final Document Status per User: Solution

**Difficulty:** 🟡 Medium &nbsp;|&nbsp; **Concepts used:** CTE, ROW_NUMBER() with PARTITION BY, latest row per group, pivot with MAX(CASE), COALESCE()

> [📄 Question](Question.md) &nbsp;|&nbsp; 💡 **Solution** &nbsp;|&nbsp; [🏠 All questions](../README.md)

## 🧾 Solution Code (MySQL 8.0+)

```sql
WITH latest AS (
    SELECT user_id,
           doc_type,
           status,
           ROW_NUMBER() OVER (PARTITION BY user_id, doc_type
                              ORDER BY updated_at DESC) AS rn
    FROM kyc_documents
)
SELECT user_id,
       COALESCE(MAX(CASE WHEN doc_type = 'PAN'     THEN status END), 'Not uploaded') AS pan_status,
       COALESCE(MAX(CASE WHEN doc_type = 'AADHAAR' THEN status END), 'Not uploaded') AS aadhaar_status
FROM latest
WHERE rn = 1
GROUP BY user_id
ORDER BY user_id;
```

## 🪜 Step-by-Step Explanation

**Step 1: number the updates of each document, newest first (`ROW_NUMBER`)**

```sql
ROW_NUMBER() OVER (PARTITION BY user_id, doc_type ORDER BY updated_at DESC) AS rn
```
- `ROW_NUMBER()` gives rows the numbers 1, 2, 3 … with no ties.
- `PARTITION BY user_id, doc_type` restarts the numbering for every user-and-document combination (u3-PAN separately from u3-AADHAAR).
- `ORDER BY updated_at DESC` sorts newest first, so **rn = 1 is always the latest update**.

```text
+---------+----------+------------+-------------------------+----+
| user_id | doc_type | updated_at | status                  | rn |
+---------+----------+------------+-------------------------+----+
| u1      | AADHAAR  | 2020-02-01 | Verified                |  1 |
| u1      | PAN      | 2020-01-01 | Verified                |  1 |
| u2      | PAN      | 2020-03-01 | Rejected                |  1 |
| u3      | AADHAAR  | 2020-01-25 | Rejected                |  1 |
| u3      | PAN      | 2020-01-15 | Pending on verification |  1 |
| u3      | PAN      | 2020-01-01 | Rejected                |  2 |
+---------+----------+------------+-------------------------+----+
```

u3's PAN has two updates. The 15 Jan one is newer, so it gets rn = 1.

**Step 2: keep only the latest update (`WHERE rn = 1`)**: the old u3-PAN "Rejected" row (rn = 2) is removed. This happens outside the CTE because a window function can't be filtered in the same `WHERE` where it's calculated.

**Step 3: turn document rows into columns (pivot)**
- `GROUP BY user_id` puts each user's remaining rows into one bucket.
- `CASE WHEN doc_type = 'PAN' THEN status END` returns the status on the PAN row and **NULL** on every other row (a `CASE` with no `ELSE` returns NULL).
- `MAX(…)` picks the non-NULL value from the bucket. Aggregates ignore NULLs, so the result is the PAN status. The same is done for AADHAAR.

**Step 4: fill in missing documents (`COALESCE`)**
- u2 has no AADHAAR row, so `MAX(CASE …)` returns NULL for u2's AADHAAR.
- `COALESCE(a, b)` returns the first value that is not NULL. So `COALESCE(NULL, 'Not uploaded')` gives **'Not uploaded'**.

---

[⬅ Back to the question](Question.md) &nbsp;|&nbsp; [🏠 All questions](../README.md)
