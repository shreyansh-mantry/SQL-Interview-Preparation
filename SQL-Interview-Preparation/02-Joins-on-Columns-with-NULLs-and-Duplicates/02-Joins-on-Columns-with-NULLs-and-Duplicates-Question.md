# 📌 2) Joins on Columns with NULLs and Duplicates

**Difficulty:** 🟢 Easy

> 📄 **Question** &nbsp;|&nbsp; [💡 Solution](02-Joins-on-Columns-with-NULLs-and-Duplicates-Solution.md) &nbsp;|&nbsp; [🏠 All questions](../README.md)

## 📝 Question

```sql
/*
Tables table_a (A) and table_b (B) each have one column, name.
Both tables contain duplicate names and NULL values.

Join the two tables on A.name = B.name and write a query that returns the
number of rows produced by each of these joins:

(a) A LEFT JOIN B
(b) A RIGHT JOIN B
(c) A INNER JOIN B

Output columns: join_type, row_count
*/
```

## 🗂️ Create Table

```sql
CREATE TABLE table_a (
    name  VARCHAR(50)
);

CREATE TABLE table_b (
    name  VARCHAR(50)
);
```

## 📥 Insert Sample Data

```sql
INSERT INTO table_a (name) VALUES
('Frank'),('Angela'),('Rose'),('Patrick'),(NULL),('Patrick');

INSERT INTO table_b (name) VALUES
('Patrick'),('Patrick'),('Frank'),(NULL),('Michael'),(NULL);
```

## ✅ Expected Output

```text
+----------------+-----------+
| join_type      | row_count |
+----------------+-----------+
| A LEFT JOIN B  |         8 |
| A RIGHT JOIN B |         8 |
| A INNER JOIN B |         5 |
+----------------+-----------+
```

---

**Tried it? 👉 [See the solution](02-Joins-on-Columns-with-NULLs-and-Duplicates-Solution.md)**
