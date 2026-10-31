# 📌 1) SQL Logical Execution Order

**Difficulty:** 🟢 Easy

> 📄 **Question** &nbsp;|&nbsp; [💡 Solution](Solution.md) &nbsp;|&nbsp; [🏠 All questions](../README.md)

## 📝 Question

```sql
/*
The query below finds the most active traders in October 2026.

SELECT
    t.user_id,
    COUNT(*)        AS trade_count,
    SUM(t.amount)   AS total_amount
FROM trades t
JOIN users  u
  ON t.user_id = u.user_id
WHERE t.status = 'executed'
  AND t.trade_date >= '2026-10-01'
GROUP BY t.user_id
HAVING COUNT(*) > 5
ORDER BY total_amount DESC
LIMIT 10;

(a) In what order does SQL logically process the clauses of this query?
(b) What does the query return on the sample data?
(c) Why is user_id written as t.user_id in the SELECT and GROUP BY clauses?
*/
```

## 🗂️ Create Table

```sql
CREATE TABLE users (
    user_id    VARCHAR(5),
    user_name  VARCHAR(30)
);

CREATE TABLE trades (
    trade_id    VARCHAR(5),
    user_id     VARCHAR(5),
    trade_date  DATE,
    amount      INT,
    status      VARCHAR(15)
);
```

## 📥 Insert Sample Data

```sql
INSERT INTO users (user_id, user_name) VALUES
('U1','Aarav'),('U2','Diya'),('U3','Kabir'),('U4','Meera'),('U5','Rohan');

INSERT INTO trades (trade_id, user_id, trade_date, amount, status) VALUES
('T01','U1','2026-10-02',1000,'executed'), ('T02','U1','2026-10-03',2000,'executed'),
('T03','U1','2026-10-04',1500,'executed'), ('T04','U1','2026-10-05',500,'executed'),
('T05','U1','2026-10-06',3000,'executed'), ('T06','U1','2026-10-07',2500,'executed'),
('T07','U1','2026-10-08',1000,'executed'), ('T08','U2','2026-10-03',4000,'executed'),
('T09','U2','2026-10-04',1000,'executed'), ('T10','U2','2026-10-05',2000,'executed'),
('T11','U2','2026-10-06',3000,'executed'), ('T12','U2','2026-10-07',1500,'executed'),
('T13','U2','2026-10-08',500,'executed'),  ('T14','U2','2026-10-10',9000,'cancelled'),
('T15','U3','2026-10-04',5000,'executed'), ('T16','U3','2026-10-05',5000,'executed'),
('T17','U3','2026-10-06',5000,'executed'), ('T18','U3','2026-10-07',5000,'executed'),
('T19','U3','2026-10-08',5000,'executed'), ('T20','U4','2026-09-20',700,'executed'),
('T21','U4','2026-09-25',900,'executed'),  ('T22','U4','2026-10-05',600,'executed'),
('T23','U4','2026-10-06',600,'executed'),  ('T24','U4','2026-10-07',600,'executed'),
('T25','U4','2026-10-08',600,'executed'),  ('T26','U5','2026-10-06',800,'executed'),
('T27','U5','2026-10-07',800,'executed'),  ('T28','U5','2026-10-08',800,'executed'),
('T29','U5','2026-10-09',800,'executed'),  ('T30','U5','2026-10-10',800,'executed'),
('T31','U5','2026-10-11',800,'executed'),  ('T32','U5','2026-10-12',800,'executed'),
('T33','U5','2026-10-13',800,'executed');
```

## ✅ Expected Output

**(b) Query result**

```text
+---------+-------------+--------------+
| user_id | trade_count | total_amount |
+---------+-------------+--------------+
| U2      |           6 |        12000 |
| U1      |           7 |        11500 |
| U5      |           8 |         6400 |
+---------+-------------+--------------+
```

---

**Tried it? 👉 [See the solution](Solution.md)**
