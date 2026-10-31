# 📌 7) Investment Snapshot (Successful Transactions Only)

**Difficulty:** 🟡 Medium

> 📄 **Question** &nbsp;|&nbsp; [💡 Solution](Solution.md) &nbsp;|&nbsp; [🏠 All questions](../README.md)

## 📝 Question

```sql
/*
The orders table has every order a user placed (First Order or Repeat Order) and
the transactions table has the status and amount of each order's transaction.

Write a query that gives a snapshot of each user's investments, counting only
successful transactions, with these columns:

  user_id
  first_order_success_flag    -> 1 if the user's first order succeeded, else 0
  repeat_order_success_count  -> number of successful repeat orders
  total_amount_invested       -> total amount across all successful transactions

Every user must appear in the output, including users with no successful
transaction (show 0 for them).
*/
```

## 🗂️ Create Table

```sql
CREATE TABLE orders (
    user_id     VARCHAR(5),
    txn_id      VARCHAR(5),
    order_date  DATE,
    order_kind  VARCHAR(20)
);

CREATE TABLE transactions (
    txn_id  VARCHAR(5),
    status  VARCHAR(20),
    amount  INT
);
```

## 📥 Insert Sample Data

```sql
INSERT INTO orders (user_id, txn_id, order_date, order_kind) VALUES
('A','Y12','2020-01-12','First Order'),('B','Y13','2020-02-01','First Order'),
('D','Y14','2020-01-07','First Order'),('A','Y15','2020-01-18','Repeat Order'),
('C','Y16','2020-01-20','First Order'),('B','Y17','2020-01-25','Repeat Order'),
('B','Y18','2020-01-26','Repeat Order'),('D','Y19','2020-02-10','Repeat Order'),
('D','Y20','2020-02-11','Repeat Order'),('D','Y21','2020-02-12','Repeat Order'),
('D','Y22','2020-02-13','Repeat Order');

INSERT INTO transactions (txn_id, status, amount) VALUES
('Y12','Success',500),('Y13','Rejected',5000),('Y14','Success',100),
('Y15','Success',250),('Y16','Rejected',700),('Y17','Success',200),
('Y18','Success',2500),('Y19','Success',500),('Y20','Success',2000),
('Y21','Success',200),('Y22','Rejected',400);
```

## ✅ Expected Output

```text
+---------+--------------------------+----------------------------+-----------------------+
| user_id | first_order_success_flag | repeat_order_success_count | total_amount_invested |
+---------+--------------------------+----------------------------+-----------------------+
| A       |                        1 |                          1 |                   750 |
| B       |                        0 |                          2 |                  2700 |
| C       |                        0 |                          0 |                     0 |
| D       |                        1 |                          3 |                  2800 |
+---------+--------------------------+----------------------------+-----------------------+
```

---

**Tried it? 👉 [See the solution](Solution.md)**
