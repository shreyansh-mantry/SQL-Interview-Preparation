# 💡 4) IPL Match Fixtures: Solution

**Difficulty:** 🟢 Easy &nbsp;|&nbsp; **Concepts used:** SELF JOIN, table aliases, non-equi join (< and <>), COUNT(*), UNION ALL

> [📄 Question](Question.md) &nbsp;|&nbsp; 💡 **Solution** &nbsp;|&nbsp; [🏠 All questions](../README.md)

## 🧾 Solution Code (MySQL 8.0+)

```sql
SELECT '(a) Each pair plays once'  AS schedule,
       COUNT(*)                    AS total_matches
FROM ipl_teams t1
JOIN ipl_teams t2
  ON t1.team_symbol < t2.team_symbol

UNION ALL

SELECT '(b) Each pair plays twice' AS schedule,
       COUNT(*)                    AS total_matches
FROM ipl_teams t1
JOIN ipl_teams t2
  ON t1.team_symbol <> t2.team_symbol;
```

## 🪜 Step-by-Step Explanation

**Step 1: join the table to itself (a SELF JOIN)**
- A match needs **two** teams, but they both come from the same table. So we read `ipl_teams` twice and give each copy its own alias: `t1` (first team) and `t2` (second team).
- Without any condition, joining 10 teams with 10 teams gives 10 × 10 = **100 pairs**. That includes nonsense pairs like *CSK vs CSK*, and every pair appears twice (*CSK vs MI* and *MI vs CSK*).

**Step 2: choose the right `ON` condition**

| Condition | What it keeps | Rows |
|---|---|---|
| `t1.team_symbol <> t2.team_symbol` | every pair of *different* teams, in both orders | 100 − 10 = **90** |
| `t1.team_symbol < t2.team_symbol` | every pair of different teams, **only once** | 90 ÷ 2 = **45** |

- `<>` means "not equal". It removes only the 10 *team vs itself* rows. *CSK vs MI* and *MI vs CSK* both stay, which is exactly a home match and an away match, so it answers **(b)**.
- `<` compares the symbols alphabetically. For any two teams, only one order passes (`'CSK' < 'MI'` is true, `'MI' < 'CSK'` is false), so each pair appears once, which answers **(a)**.
- `=` would be wrong: it keeps *only* the 10 team-vs-itself rows.

**Step 3: count the matches**
- `COUNT(*)` counts the rows each join produces: 45 for (a), 90 for (b).
- `'(a) Each pair plays once' AS schedule` is a **string literal**: fixed text that labels each count.
- `UNION ALL` stacks the two one-row results into one 2-row output.

**Check with maths**
- Playing once is n × (n − 1) ÷ 2 = 10 × 9 ÷ 2 = **45** matches.
- Playing twice is n × (n − 1) = 10 × 9 = **90** matches.

---

**See the actual fixtures**

To list the matches instead of counting them, drop `COUNT(*)` and select the two team columns:

```sql
-- (a) Each pair plays once
SELECT t1.team_symbol AS team_1,
       t2.team_symbol AS team_2
FROM ipl_teams t1
JOIN ipl_teams t2
  ON t1.team_symbol < t2.team_symbol
ORDER BY team_1, team_2;
```

<details>
<summary>Show all 45 matches for (a)</summary>

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

```sql
-- (b) Each pair plays twice (home and away)
SELECT t1.team_symbol AS home_team,
       t2.team_symbol AS away_team
FROM ipl_teams t1
JOIN ipl_teams t2
  ON t1.team_symbol <> t2.team_symbol
ORDER BY home_team, away_team;
```

<details>
<summary>Show all 90 matches for (b)</summary>

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

In list (b), every pair appears twice: for example *CSK vs MI* (CSK at home) and *MI vs CSK* (MI at home).

---

[⬅ Back to the question](Question.md) &nbsp;|&nbsp; [🏠 All questions](../README.md)
