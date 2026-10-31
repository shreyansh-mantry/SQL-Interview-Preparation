# 💡 2) Joins on Columns with NULLs and Duplicates: Solution

**Difficulty:** 🟢 Easy &nbsp;|&nbsp; **Concepts used:** INNER / LEFT / RIGHT JOIN, NULL comparison, duplicate join keys

> [📄 Question](Question.md) &nbsp;|&nbsp; 💡 **Solution** &nbsp;|&nbsp; [🏠 All questions](../README.md)

## 🧾 Solution Code (MySQL 8.0+)

**(a) A LEFT JOIN B**

```sql
SELECT a.name AS a_name,
       b.name AS b_name
FROM table_a a
LEFT JOIN table_b b
       ON a.name = b.name;
```

**(b) A RIGHT JOIN B**

```sql
SELECT a.name AS a_name,
       b.name AS b_name
FROM table_a a
RIGHT JOIN table_b b
        ON a.name = b.name;
```

**(c) A INNER JOIN B**

```sql
SELECT a.name AS a_name,
       b.name AS b_name
FROM table_a a
INNER JOIN table_b b
        ON a.name = b.name;
```

## 🪜 Step-by-Step Explanation

**What each join does**
- **INNER JOIN** returns only the pairs of rows where the `ON` condition is true, meaning a match exists in *both* tables.
- **LEFT JOIN** returns *every* row of the left table (A). If an A row has no match in B, it still appears, with NULL in B's columns.
- **RIGHT JOIN** is the mirror: *every* row of the right table (B) appears, with NULL in A's columns when there's no match.

**Two rules decide the row counts here**

**Rule 1: duplicates multiply.** Each row in A is compared with *every* row in B. A has 2 × `Patrick` and B has 2 × `Patrick`, so every A-Patrick matches both B-Patricks: 2 × 2 = **4 Patrick rows**.

**Rule 2: NULL never matches anything, not even another NULL.** `NULL` means "unknown", and `NULL = NULL` evaluates to *unknown*, not true. So the NULL rows never find a partner.

**(a) LEFT JOIN, row by row through A**

| A row | Matches in B | Rows produced |
|---|---|---|
| Frank | Frank | 1 |
| Angela | none | 1 (Angela, NULL) |
| Rose | none | 1 (Rose, NULL) |
| Patrick | Patrick, Patrick | 2 |
| NULL | none | 1 (NULL, NULL) |
| Patrick | Patrick, Patrick | 2 |
| **Total** | | **8** |

**(b) RIGHT JOIN, row by row through B:** Patrick → 2, Patrick → 2, Frank → 1, NULL → 1 (no match), Michael → 1 (no match), NULL → 1 (no match). **Total 8.**

**(c) INNER JOIN:** only real matches are kept: 4 Patrick rows + 1 Frank row = **5**.

> **Tip:** the `(NULL, NULL)` row in the LEFT JOIN doesn't mean the NULLs matched. It is A's NULL row that found *no* match, so B's side is filled with NULL.

---

[⬅ Back to the question](Question.md) &nbsp;|&nbsp; [🏠 All questions](../README.md)
