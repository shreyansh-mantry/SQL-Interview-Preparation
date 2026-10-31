# 📌 13) Longest Ride Streak

**Difficulty:** 🔴 Hard

> 📄 **Question** &nbsp;|&nbsp; [💡 Solution](Solution.md) &nbsp;|&nbsp; [🏠 All questions](../README.md)

## 📝 Question

```sql
/*
The rides table has every ride a user took. A streak is a run of consecutive
calendar days on which the user took at least one ride.

Write a query that returns each user's maximum streak: the length (in days) of
the longest streak the user ever had.

Output columns: user_id, max_streak
*/
```

## 🗂️ Create Table

```sql
CREATE TABLE rides (
    ride_date  DATE,
    ride_id    VARCHAR(5),
    user_id    VARCHAR(5)
);
```

## 📥 Insert Sample Data

```sql
INSERT INTO rides (ride_date, ride_id, user_id) VALUES
('2020-02-19','X0','A'),('2020-02-20','X1','A'),('2020-02-21','X2','A'),
('2020-02-24','X3','A'),('2020-02-25','X4','A'),('2020-02-26','X5','A'),
('2020-02-27','X6','A'),
('2020-02-18','Y0','B'),('2020-02-19','Y1','B'),('2020-02-20','Y2','B'),
('2020-02-21','Y3','B'),('2020-02-22','Y4','B'),('2020-02-22','Y5','B'),
('2020-02-26','Y6','B'),('2020-02-27','Y7','B');
```

## ✅ Expected Output

```text
+---------+------------+
| user_id | max_streak |
+---------+------------+
| A       |          4 |
| B       |          5 |
+---------+------------+
```

---

**Tried it? 👉 [See the solution](Solution.md)**
