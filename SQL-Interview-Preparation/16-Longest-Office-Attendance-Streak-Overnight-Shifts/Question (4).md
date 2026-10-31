# 📌 16) Longest Office Attendance Streak (Overnight Shifts)

**Difficulty:** 🔴 Hard

> 📄 **Question** &nbsp;|&nbsp; [💡 Solution](Solution.md) &nbsp;|&nbsp; [🏠 All questions](../README.md)

## 📝 Question

```sql
/*
The employee_attendance table has one row per office session with the login time
and logout time. A session can run past midnight (for example: login 10 pm,
logout 6 am the next day), but never lasts longer than 24 hours.

The employee is present on every calendar day on which they logged in or logged
out, and is on leave on any day with no session.

Write a query that returns each employee's longest streak of consecutive days
present in office:

  employee_id
  login_date       -> first day of the streak
  logout_date      -> last day of the streak
  max_streak_days  -> number of consecutive days in the streak
*/
```

## 🗂️ Create Table

```sql
CREATE TABLE employee_attendance (
    employee_id  INT,
    login_time   DATETIME,
    logout_time  DATETIME
);
```

## 📥 Insert Sample Data

```sql
INSERT INTO employee_attendance (employee_id, login_time, logout_time) VALUES
(1,'2025-01-01 08:00:00','2025-01-01 16:00:00'),
(1,'2025-01-02 08:00:00','2025-01-02 16:00:00'),
(1,'2025-01-02 22:00:00','2025-01-03 06:00:00'),
(1,'2025-01-04 08:00:00','2025-01-04 16:00:00'),
(1,'2025-01-06 08:00:00','2025-01-06 16:00:00'),
(1,'2025-01-07 08:00:00','2025-01-07 16:00:00');
```

## ✅ Expected Output

```text
+-------------+------------+-------------+-----------------+
| employee_id | login_date | logout_date | max_streak_days |
+-------------+------------+-------------+-----------------+
|           1 | 2025-01-01 | 2025-01-04  |               4 |
+-------------+------------+-------------+-----------------+
```

---

**Tried it? 👉 [See the solution](Solution.md)**
