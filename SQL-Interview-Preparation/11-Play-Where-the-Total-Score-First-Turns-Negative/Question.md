# 📌 11) Play Where the Total Score First Turns Negative

**Difficulty:** 🟡 Medium

> 📄 **Question** &nbsp;|&nbsp; [💡 Solution](Solution.md) &nbsp;|&nbsp; [🏠 All questions](../README.md)

## 📝 Question

```sql
/*
The plays table has every play of each user with the score earned in that play.
A user's total score is the running sum of scores over their plays, in time order.

Write a query that returns the user_id, play_id, play_timestamp and score of the
play at which a user's total score first becomes negative.
Users whose total score never becomes negative should not appear.
*/
```

## 🗂️ Create Table

```sql
CREATE TABLE plays (
    user_id         INT,
    play_id         INT,
    play_timestamp  DATETIME,
    score           INT
);
```

## 📥 Insert Sample Data

```sql
INSERT INTO plays (user_id, play_id, play_timestamp, score) VALUES
(1,1234,'2025-01-01 16:00:00',100),
(1,5678,'2025-01-01 16:05:00',150),
(1,4235,'2025-01-01 16:10:00',-100),
(1,3456,'2025-01-01 16:12:00',-250),
(1,8654,'2025-01-01 16:15:00',400),
(2,6767,'2025-01-01 17:00:00',100),
(2,3476,'2025-01-01 17:05:00',200),
(2,6665,'2025-01-01 17:10:00',-150),
(2,5346,'2025-01-01 17:15:00',-50),
(2,4356,'2025-01-01 17:20:00',75),
(2,4366,'2025-01-01 17:25:00',-75);
```

## ✅ Expected Output

```text
+---------+---------+---------------------+-------+
| user_id | play_id | play_timestamp      | score |
+---------+---------+---------------------+-------+
|       1 |    3456 | 2025-01-01 16:12:00 |  -250 |
+---------+---------+---------------------+-------+
```

---

**Tried it? 👉 [See the solution](Solution.md)**
