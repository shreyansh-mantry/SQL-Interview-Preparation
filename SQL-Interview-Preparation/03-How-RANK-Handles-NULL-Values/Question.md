# 📌 3) How RANK() Handles NULL Values

**Difficulty:** 🟢 Easy

> 📄 **Question** &nbsp;|&nbsp; [💡 Solution](Solution.md) &nbsp;|&nbsp; [🏠 All questions](../README.md)

## 📝 Question

```sql
/*
The department_salary table has 5 departments. Two of them have a NULL salary.

Using the RANK() window function, rank the departments by salary and
explain how the departments with a NULL salary are ranked:

(a) highest salary first
(b) lowest salary first
(c) lowest salary first, with the NULL salaries ranked at the bottom
*/
```

## 🗂️ Create Table

```sql
CREATE TABLE department_salary (
    department  VARCHAR(20),
    salary      INT
);
```

## 📥 Insert Sample Data

```sql
INSERT INTO department_salary (department, salary) VALUES
('Finance',1000),('Sales',500),('Business',1500),('Marketing',NULL),('HR',NULL);
```

## ✅ Expected Output

**(a) Highest salary first**

```text
+------------+--------+-------------+
| department | salary | salary_rank |
+------------+--------+-------------+
| Business   |   1500 |           1 |
| Finance    |   1000 |           2 |
| Sales      |    500 |           3 |
| HR         |   NULL |           4 |
| Marketing  |   NULL |           4 |
+------------+--------+-------------+
```

**(b) Lowest salary first**

```text
+------------+--------+-------------+
| department | salary | salary_rank |
+------------+--------+-------------+
| HR         |   NULL |           1 |
| Marketing  |   NULL |           1 |
| Sales      |    500 |           3 |
| Finance    |   1000 |           4 |
| Business   |   1500 |           5 |
+------------+--------+-------------+
```

**(c) Lowest first, NULLs at the bottom**

```text
+------------+--------+-------------+
| department | salary | salary_rank |
+------------+--------+-------------+
| Sales      |    500 |           1 |
| Finance    |   1000 |           2 |
| Business   |   1500 |           3 |
| HR         |   NULL |           4 |
| Marketing  |   NULL |           4 |
+------------+--------+-------------+
```

---

**Tried it? 👉 [See the solution](Solution.md)**
