# 💡 2) Joins on Columns with NULLs and Duplicates: Solution

**Difficulty:** 🟢 Easy &nbsp;|&nbsp; **Concepts used:** INNER / LEFT / RIGHT JOIN, NULL comparison, duplicate join keys, COUNT(*), UNION ALL

> [📄 Question](02-Joins-on-Columns-with-NULLs-and-Duplicates-Question.md) &nbsp;|&nbsp; 💡 **Solution** &nbsp;|&nbsp; [🏠 All questions](../README.md)

## 🧾 Solution Code (MySQL 8.0+)

```sql
SELECT 'A LEFT JOIN B'  AS join_type,
       COUNT(*)         AS row_count
FROM table_a a
LEFT JOIN table_b b
       ON a.name = b.name

UNION ALL

SELECT 'A RIGHT JOIN B' AS join_type,
       COUNT(*)         AS row_count
FROM table_a a
RIGHT JOIN table_b b
        ON a.name = b.name

UNION ALL

SELECT 'A INNER JOIN B' AS join_type,
       COUNT(*)         AS row_count
FROM table_a a
INNER JOIN table_b b
        ON a.name = b.name;
```

## 🪜 Step-by-Step Explanation

**How the query is built**
- Each of the three `SELECT`s runs one join and counts the rows it produces.
  - `COUNT(*)` counts every row the join returns, including rows that contain NULLs.
  - `'A LEFT JOIN B' AS join_type` is a **string literal**: fixed text printed in the output so we know which count belongs to which join.
- `UNION ALL` stacks the three one-row results into a single 3-row table.

**What each join does**
- **INNER JOIN** returns only the pairs of rows where the `ON` condition is true, meaning a match exists in *both* tables.
- **LEFT JOIN** returns *every* row of the left table (A). If an A row has no match in B, it still appears, with NULL in B's columns.
- **RIGHT JOIN** is the mirror: *every* row of the right table (B) appears, with NULL in A's columns when there's no match.

**Two rules decide the row counts**

**Rule 1: duplicates multiply.** Each row in A is compared with *every* row in B. A has 2 × `Patrick` and B has 2 × `Patrick`, so every A-Patrick matches both B-Patricks: 2 × 2 = **4 Patrick rows**.

**Rule 2: NULL never matches anything, not even another NULL.** `NULL` means "unknown", and `NULL = NULL` evaluates to *unknown*, not true. So the NULL rows never find a partner.

---

**(a) A LEFT JOIN B = 8 rows**

Going through A row by row:

| A row | Matches in B | Rows produced |
|---|---|---|
| Frank | Frank | 1 |
| Angela | none | 1 (Angela, NULL) |
| Rose | none | 1 (Rose, NULL) |
| Patrick | Patrick, Patrick | 2 |
| NULL | none | 1 (NULL, NULL) |
| Patrick | Patrick, Patrick | 2 |
| **Total** | | **8** |

The rows themselves:

```text
+---------+---------+
| a_name  | b_name  |
+---------+---------+
| Patrick | Patrick |
| Patrick | Patrick |
| Patrick | Patrick |
| Patrick | Patrick |
| Frank   | Frank   |
| Angela  | NULL    |
| Rose    | NULL    |
| NULL    | NULL    |
+---------+---------+
```

The `(NULL, NULL)` row doesn't mean the NULLs matched. It's A's NULL row that found *no* match, so B's side is filled with NULL.

---

**(b) A RIGHT JOIN B = 8 rows**

Going through B row by row:

| B row | Matches in A | Rows produced |
|---|---|---|
| Patrick | Patrick, Patrick | 2 |
| Patrick | Patrick, Patrick | 2 |
| Frank | Frank | 1 |
| NULL | none | 1 (NULL, NULL) |
| Michael | none | 1 (NULL, Michael) |
| NULL | none | 1 (NULL, NULL) |
| **Total** | | **8** |

The rows themselves:

```text
+---------+---------+
| a_name  | b_name  |
+---------+---------+
| Frank   | Frank   |
| Patrick | Patrick |
| Patrick | Patrick |
| Patrick | Patrick |
| Patrick | Patrick |
| NULL    | NULL    |
| NULL    | Michael |
| NULL    | NULL    |
+---------+---------+
```

---

**(c) A INNER JOIN B = 5 rows**

Only real matches are kept: 4 Patrick rows (2 × 2) + 1 Frank row = **5**. Angela, Rose, Michael and all the NULLs have no partner, so they're dropped.

```text
+---------+---------+
| a_name  | b_name  |
+---------+---------+
| Patrick | Patrick |
| Patrick | Patrick |
| Patrick | Patrick |
| Patrick | Patrick |
| Frank   | Frank   |
+---------+---------+
```

---

**Quick formula to check your counts**
- **INNER** = matched pairs = 4 + 1 = **5**
- **LEFT** = INNER + unmatched A rows (Angela, Rose, NULL) = 5 + 3 = **8**
- **RIGHT** = INNER + unmatched B rows (NULL, Michael, NULL) = 5 + 3 = **8**

> **Interview follow-up:** a **FULL OUTER JOIN** would return INNER + unmatched A + unmatched B = 5 + 3 + 3 = **11** rows. MySQL has no `FULL OUTER JOIN` keyword; it's usually written as a `LEFT JOIN` combined with the unmatched rows of a `RIGHT JOIN` using `UNION ALL`.

> **Note:** the row order of a join without `ORDER BY` isn't guaranteed, so the rows may appear in a different order in your database. The counts will always be the same.

---

[⬅ Back to the question](02-Joins-on-Columns-with-NULLs-and-Duplicates-Question.md) &nbsp;|&nbsp; [🏠 All questions](../README.md)
