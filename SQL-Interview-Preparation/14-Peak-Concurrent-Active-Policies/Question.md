# 📌 14) Peak Concurrent Active Policies

**Difficulty:** 🔴 Hard

> 📄 **Question** &nbsp;|&nbsp; [💡 Solution](Solution.md) &nbsp;|&nbsp; [🏠 All questions](../README.md)

## 📝 Question

```sql
/*
The policies table has each user's insurance policy with a start date and an end
date. A policy is active on every day from its start date to its end date, both
days included.

Write a query that finds the date on which the maximum number of policies were
active at the same time, and that maximum number.

Output columns: peak_date, max_concurrent_policies
*/
```

## 🗂️ Create Table

```sql
CREATE TABLE policies (
    user_id            INT,
    policy_start_date  DATE,
    policy_end_date    DATE
);
```

## 📥 Insert Sample Data

```sql
INSERT INTO policies (user_id, policy_start_date, policy_end_date) VALUES
(101,'2025-01-01','2025-01-10'),
(102,'2025-01-03','2025-01-06'),
(103,'2025-01-05','2025-01-12'),
(104,'2025-01-05','2025-01-08'),
(105,'2025-01-09','2025-01-15'),
(106,'2025-01-11','2025-01-20'),
(107,'2025-01-14','2025-01-18');
```

## ✅ Expected Output

```text
+------------+-------------------------+
| peak_date  | max_concurrent_policies |
+------------+-------------------------+
| 2025-01-05 |                       4 |
+------------+-------------------------+
```

---

**Tried it? 👉 [See the solution](Solution.md)**
