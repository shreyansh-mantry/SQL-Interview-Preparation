# 📌 4) IPL Match Fixtures

**Difficulty:** 🟢 Easy

> 📄 **Question** &nbsp;|&nbsp; [💡 Solution](Solution.md) &nbsp;|&nbsp; [🏠 All questions](../README.md)

## 📝 Question

```sql
/*
The ipl_teams table has the 10 IPL teams.

Write a query to list all the matches of the season if:
(a) every team plays every other team only once
(b) every team plays every other team twice in the season (home and away)
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

**(a) Every team plays every other team once: 45 matches**

<details>
<summary>Show all 45 rows</summary>

```text
+--------+--------+
| team_1 | team_2 |
+--------+--------+
| CSK    | DC     |
| CSK    | GT     |
| CSK    | KKR    |
| CSK    | LSG    |
| CSK    | MI     |
| CSK    | PBKS   |
| CSK    | RCB    |
| CSK    | RR     |
| CSK    | SRH    |
| DC     | GT     |
| DC     | KKR    |
| DC     | LSG    |
| DC     | MI     |
| DC     | PBKS   |
| DC     | RCB    |
| DC     | RR     |
| DC     | SRH    |
| GT     | KKR    |
| GT     | LSG    |
| GT     | MI     |
| GT     | PBKS   |
| GT     | RCB    |
| GT     | RR     |
| GT     | SRH    |
| KKR    | LSG    |
| KKR    | MI     |
| KKR    | PBKS   |
| KKR    | RCB    |
| KKR    | RR     |
| KKR    | SRH    |
| LSG    | MI     |
| LSG    | PBKS   |
| LSG    | RCB    |
| LSG    | RR     |
| LSG    | SRH    |
| MI     | PBKS   |
| MI     | RCB    |
| MI     | RR     |
| MI     | SRH    |
| PBKS   | RCB    |
| PBKS   | RR     |
| PBKS   | SRH    |
| RCB    | RR     |
| RCB    | SRH    |
| RR     | SRH    |
+--------+--------+
```

</details>

**(b) Every team plays every other team twice: 90 matches**

<details>
<summary>Show all 90 rows</summary>

```text
+-----------+-----------+
| home_team | away_team |
+-----------+-----------+
| CSK       | DC        |
| CSK       | GT        |
| CSK       | KKR       |
| CSK       | LSG       |
| CSK       | MI        |
| CSK       | PBKS      |
| CSK       | RCB       |
| CSK       | RR        |
| CSK       | SRH       |
| DC        | CSK       |
| DC        | GT        |
| DC        | KKR       |
| DC        | LSG       |
| DC        | MI        |
| DC        | PBKS      |
| DC        | RCB       |
| DC        | RR        |
| DC        | SRH       |
| GT        | CSK       |
| GT        | DC        |
| GT        | KKR       |
| GT        | LSG       |
| GT        | MI        |
| GT        | PBKS      |
| GT        | RCB       |
| GT        | RR        |
| GT        | SRH       |
| KKR       | CSK       |
| KKR       | DC        |
| KKR       | GT        |
| KKR       | LSG       |
| KKR       | MI        |
| KKR       | PBKS      |
| KKR       | RCB       |
| KKR       | RR        |
| KKR       | SRH       |
| LSG       | CSK       |
| LSG       | DC        |
| LSG       | GT        |
| LSG       | KKR       |
| LSG       | MI        |
| LSG       | PBKS      |
| LSG       | RCB       |
| LSG       | RR        |
| LSG       | SRH       |
| MI        | CSK       |
| MI        | DC        |
| MI        | GT        |
| MI        | KKR       |
| MI        | LSG       |
| MI        | PBKS      |
| MI        | RCB       |
| MI        | RR        |
| MI        | SRH       |
| PBKS      | CSK       |
| PBKS      | DC        |
| PBKS      | GT        |
| PBKS      | KKR       |
| PBKS      | LSG       |
| PBKS      | MI        |
| PBKS      | RCB       |
| PBKS      | RR        |
| PBKS      | SRH       |
| RCB       | CSK       |
| RCB       | DC        |
| RCB       | GT        |
| RCB       | KKR       |
| RCB       | LSG       |
| RCB       | MI        |
| RCB       | PBKS      |
| RCB       | RR        |
| RCB       | SRH       |
| RR        | CSK       |
| RR        | DC        |
| RR        | GT        |
| RR        | KKR       |
| RR        | LSG       |
| RR        | MI        |
| RR        | PBKS      |
| RR        | RCB       |
| RR        | SRH       |
| SRH       | CSK       |
| SRH       | DC        |
| SRH       | GT        |
| SRH       | KKR       |
| SRH       | LSG       |
| SRH       | MI        |
| SRH       | PBKS      |
| SRH       | RCB       |
| SRH       | RR        |
+-----------+-----------+
```

</details>

---

**Tried it? 👉 [See the solution](Solution.md)**
