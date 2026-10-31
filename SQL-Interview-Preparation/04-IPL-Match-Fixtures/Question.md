# 📌 4) IPL Match Fixtures

**Difficulty:** 🟢 Easy

> 📄 **Question** &nbsp;|&nbsp; [💡 Solution](Solution.md) &nbsp;|&nbsp; [🏠 All questions](../README.md)

## 📝 Question

```sql
/*
The ipl_teams table has the 10 IPL teams.

Write a query that returns the total number of matches in the season if:
(a) every team plays every other team only once
(b) every team plays every other team twice in the season (home and away)

Output columns: schedule, total_matches
*/
```

## 🗂️ Create Table

```sql
CREATE TABLE ipl_teams (
    team_symbol  VARCHAR(5),
    team_name    VARCHAR(50)
);
```

## 📥 Insert Sample Data

```sql
INSERT INTO ipl_teams (team_symbol, team_name) VALUES
('RCB','Royal Challengers Bangalore'),('KKR','Kolkata Knight Riders'),
('CSK','Chennai Super Kings'),('SRH','Sunrisers Hyderabad'),
('DC','Delhi Capitals'),('PBKS','Punjab Kings'),
('MI','Mumbai Indians'),('RR','Rajasthan Royals'),
('LSG','Lucknow Super Giants'),('GT','Gujarat Titans');
```

## ✅ Expected Output

```text
+---------------------------+---------------+
| schedule                  | total_matches |
+---------------------------+---------------+
| (a) Each pair plays once  |            45 |
| (b) Each pair plays twice |            90 |
+---------------------------+---------------+
```

---

**Tried it? 👉 [See the solution](Solution.md)**
