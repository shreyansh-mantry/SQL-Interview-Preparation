# 💡 4) IPL Match Fixtures: Solution

**Difficulty:** 🟢 Easy &nbsp;|&nbsp; **Concepts used:** SELF JOIN, table aliases, non-equi join (< and <>)

> [📄 Question](Question.md) &nbsp;|&nbsp; 💡 **Solution** &nbsp;|&nbsp; [🏠 All questions](../README.md)

## 🧾 Solution Code (MySQL 8.0+)

**(a) Each pair plays once**

```sql
SELECT t1.team_symbol AS team_1,
       t2.team_symbol AS team_2
FROM ipl_teams t1
JOIN ipl_teams t2
  ON t1.team_symbol < t2.team_symbol
ORDER BY team_1, team_2;
```

**(b) Each pair plays twice**

```sql
SELECT t1.team_symbol AS home_team,
       t2.team_symbol AS away_team
FROM ipl_teams t1
JOIN ipl_teams t2
  ON t1.team_symbol <> t2.team_symbol
ORDER BY home_team, away_team;
```

## 🪜 Step-by-Step Explanation

**Step 1: join the table to itself (a SELF JOIN)**
- A match needs **two** teams, but they both come from the same table. So we read `ipl_teams` twice and give each copy its own alias: `t1` (first team) and `t2` (second team).
- Without any condition, joining 10 teams with 10 teams gives 10 × 10 = 100 pairs. That includes nonsense pairs like *CSK vs CSK*, and every pair appears twice (*CSK vs MI* and *MI vs CSK*).

**Step 2: choose the right `ON` condition**

| Condition | What it keeps | Rows |
|---|---|---|
| `t1.team_symbol <> t2.team_symbol` | every pair of *different* teams, in both orders | 10 × 9 = **90** |
| `t1.team_symbol < t2.team_symbol` | every pair of different teams, **only once** | 90 ÷ 2 = **45** |

- `<>` means "not equal". It removes only the *team vs itself* rows. *CSK vs MI* and *MI vs CSK* both stay, which is exactly a home match and an away match, so it answers **(b)**.
- `<` compares the symbols alphabetically. For any two teams, only one order passes (`'CSK' < 'MI'` is true, `'MI' < 'CSK'` is false), so each pair appears once, which answers **(a)**.

**Step 3: `ORDER BY`** sorts the list so it's easy to read.

> **Check with maths:** n teams playing once is n × (n − 1) ÷ 2 = 10 × 9 ÷ 2 = 45 matches. Playing twice is n × (n − 1) = 90 matches.

---

[⬅ Back to the question](Question.md) &nbsp;|&nbsp; [🏠 All questions](../README.md)
