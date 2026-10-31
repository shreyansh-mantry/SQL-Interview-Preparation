# 📌 12) DAU, Last 7 Days and Last 30 Days Unique Users

**Difficulty:** 🟡 Medium

> 📄 **Question** &nbsp;|&nbsp; [💡 Solution](Solution.md) &nbsp;|&nbsp; [🏠 All questions](../README.md)

## 📝 Question

```sql
/*
The user_logins table has one row each time a user logged in.

Write a query that returns, for each date on which there was a login:
  activity_date
  dau                 -> unique users who logged in on that date
  last_7_days_users   -> unique users in the 7 days ending on that date (inclusive)
  last_30_days_users  -> unique users in the 30 days ending on that date (inclusive)
*/
```

## 🗂️ Create Table

```sql
CREATE TABLE user_logins (
    user_id     VARCHAR(5),
    login_date  DATE
);
```

## 📥 Insert Sample Data

```sql
INSERT INTO user_logins (user_id, login_date) VALUES
('u1','2025-01-01'),('u1','2025-01-02'),('u1','2025-01-03'),
('u2','2025-01-02'),('u2','2025-01-03'),('u3','2025-01-03'),
('u4','2025-01-10'),('u5','2025-01-20'),('u1','2025-01-25'),
('u2','2025-01-25'),('u6','2025-01-31');
```

## ✅ Expected Output

```text
+---------------+-----+-------------------+--------------------+
| activity_date | dau | last_7_days_users | last_30_days_users |
+---------------+-----+-------------------+--------------------+
| 2025-01-01    |   1 |                 1 |                  1 |
| 2025-01-02    |   2 |                 2 |                  2 |
| 2025-01-03    |   3 |                 3 |                  3 |
| 2025-01-10    |   1 |                 1 |                  4 |
| 2025-01-20    |   1 |                 1 |                  5 |
| 2025-01-25    |   2 |                 3 |                  5 |
| 2025-01-31    |   1 |                 3 |                  6 |
+---------------+-----+-------------------+--------------------+
```

---

**Tried it? 👉 [See the solution](Solution.md)**
