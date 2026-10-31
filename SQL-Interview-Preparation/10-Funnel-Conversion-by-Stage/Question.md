# 📌 10) Funnel Conversion by Stage

**Difficulty:** 🟡 Medium

> 📄 **Question** &nbsp;|&nbsp; [💡 Solution](Solution.md) &nbsp;|&nbsp; [🏠 All questions](../README.md)

## 📝 Question

```sql
/*
The funnel_events table has one row for each stage a user reached.
The stages, in order, are:
  Signup -> Account Started -> Account Created -> Purchase Done

Write a query that returns, for each stage:
  stage
  users            -> number of distinct users who reached the stage
  pct_of_total     -> users as a % of the users at Signup (rounded to 1 decimal)
  step_conversion  -> users as a % of the users at the previous stage
                      (rounded to 1 decimal; NULL for the first stage)

Order the output in funnel order.
*/
```

## 🗂️ Create Table

```sql
CREATE TABLE funnel_events (
    user_id  INT,
    stage    VARCHAR(30)
);
```

## 📥 Insert Sample Data

```sql
INSERT INTO funnel_events (user_id, stage) VALUES
(1,'Signup'),(1,'Account Started'),(1,'Account Created'),(1,'Purchase Done'),
(2,'Signup'),(2,'Account Started'),(2,'Account Created'),
(3,'Signup'),(3,'Account Started'),
(4,'Signup'),
(5,'Signup'),(5,'Account Started'),(5,'Account Created'),(5,'Purchase Done'),
(6,'Signup'),(6,'Account Started'),(6,'Account Created'),
(7,'Signup');
```

## ✅ Expected Output

```text
+-----------------+-------+--------------+-----------------+
| stage           | users | pct_of_total | step_conversion |
+-----------------+-------+--------------+-----------------+
| Signup          |     7 |        100.0 |            NULL |
| Account Started |     5 |         71.4 |            71.4 |
| Account Created |     4 |         57.1 |            80.0 |
| Purchase Done   |     2 |         28.6 |            50.0 |
+-----------------+-------+--------------+-----------------+
```

---

**Tried it? 👉 [See the solution](Solution.md)**
