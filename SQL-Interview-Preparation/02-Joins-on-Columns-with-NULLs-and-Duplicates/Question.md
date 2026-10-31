# 📌 2) Joins on Columns with NULLs and Duplicates

**Difficulty:** 🟢 Easy

> 📄 **Question** &nbsp;|&nbsp; [💡 Solution](Solution.md) &nbsp;|&nbsp; [🏠 All questions](../README.md)

## 📝 Question

```sql
/*
Tables table_a (A) and table_b (B) each have one column, name.
Join the two tables on A.name = B.name and write the output of:

(a) A LEFT JOIN B
(b) A RIGHT JOIN B
(c) A INNER JOIN B

Show the rows returned by each join and the number of rows.
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

**(a) A LEFT JOIN B: 8 rows**

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

**(b) A RIGHT JOIN B: 8 rows**

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

**(c) A INNER JOIN B: 5 rows**

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

> Row order isn't guaranteed and may differ in your database. The rows themselves will be the same.

---

**Tried it? 👉 [See the solution](Solution.md)**
