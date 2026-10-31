# 📌 6) Second-Highest Salary in Each Department

**Difficulty:** 🟡 Medium

> 📄 **Question** &nbsp;|&nbsp; [💡 Solution](Solution.md) &nbsp;|&nbsp; [🏠 All questions](../README.md)

## 📝 Question

```sql
/*
Write a query to find the employee(s) with the second-highest salary in each
department. If two or more employees have that salary, show all of them.

Output columns: department, employee_id, salary
*/
```

## 🗂️ Create Table

```sql
CREATE TABLE employees (
    department   VARCHAR(20),
    employee_id  VARCHAR(5),
    salary       INT
);
```

## 📥 Insert Sample Data

```sql
INSERT INTO employees (department, employee_id, salary) VALUES
('Finance','A',5000),('Finance','B',4500),('Finance','C',6000),
('Sales','D',4000),('Sales','E',8000),('Sales','F',7000),
('Sales','G',7000),('Sales','H',3000);
```

## ✅ Expected Output

```text
+------------+-------------+--------+
| department | employee_id | salary |
+------------+-------------+--------+
| Finance    | A           |   5000 |
| Sales      | F           |   7000 |
| Sales      | G           |   7000 |
+------------+-------------+--------+
```

---

**Tried it? 👉 [See the solution](Solution.md)**
