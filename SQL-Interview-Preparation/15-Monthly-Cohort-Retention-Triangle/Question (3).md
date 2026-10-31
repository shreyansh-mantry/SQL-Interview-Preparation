# 📌 15) Monthly Cohort Retention Triangle

**Difficulty:** 🔴 Hard

> 📄 **Question** &nbsp;|&nbsp; [💡 Solution](Solution.md) &nbsp;|&nbsp; [🏠 All questions](../README.md)

## 📝 Question

```sql
/*
The user_activity table has one row per user per active day. Data covers only
the last 4 months: Jan-Apr 2025.

A user's cohort is the month of their first activity. Build a monthly retention
triangle:

  M0 (Total Users) = the cohort's starting size (count of users).
  M1, M2, M3       = the % of M0 users who were active 1, 2, 3 months later,
                     shown with a % sign (e.g. 80%, 33.3%), rounded to 1 decimal.

Because data ends in Apr 2025, later cohorts have fewer observable months, so
future cells are blank (NULL), which gives the triangle shape.

Output columns: cohort_month, M0_total_users, M1, M2, M3
*/
```

## 🗂️ Create Table

```sql
CREATE TABLE user_activity (
    user_id        INT,
    activity_date  DATE
);
```

## 📥 Insert Sample Data

```sql
INSERT INTO user_activity (user_id, activity_date) VALUES
-- Jan cohort (users 1-5)
(1,'2025-01-05'),(1,'2025-02-05'),(1,'2025-03-05'),(1,'2025-04-05'),
(2,'2025-01-06'),(2,'2025-02-06'),(2,'2025-03-06'),(2,'2025-04-06'),
(3,'2025-01-07'),(3,'2025-02-07'),(3,'2025-03-07'),
(4,'2025-01-08'),(4,'2025-02-08'),
(5,'2025-01-09'),
-- Feb cohort (users 6-9)
(6,'2025-02-10'),(6,'2025-03-10'),(6,'2025-04-10'),
(7,'2025-02-11'),(7,'2025-03-11'),
(8,'2025-02-12'),
(9,'2025-02-13'),
-- Mar cohort (users 10-12)
(10,'2025-03-15'),(10,'2025-04-15'),
(11,'2025-03-16'),
(12,'2025-03-17'),
-- Apr cohort (users 13-16)
(13,'2025-04-20'),(14,'2025-04-21'),(15,'2025-04-22'),(16,'2025-04-23');
```

## ✅ Expected Output

```text
+--------------+----------------+-------+------+------+
| cohort_month | M0_total_users | M1    | M2   | M3   |
+--------------+----------------+-------+------+------+
| 2025-01      |              5 | 80%   | 60%  | 40%  |
| 2025-02      |              4 | 50%   | 25%  | NULL |
| 2025-03      |              3 | 33.3% | NULL | NULL |
| 2025-04      |              4 | NULL  | NULL | NULL |
+--------------+----------------+-------+------+------+
```

---

**Tried it? 👉 [See the solution](Solution.md)**
